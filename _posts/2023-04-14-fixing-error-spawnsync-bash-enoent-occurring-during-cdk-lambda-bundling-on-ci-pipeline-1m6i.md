---
#articleId: 1435838

title: "Fixing Error spawnSync bash ENOENT occurring during CDK Lambda Bundling on CI pipeline"
published: true
description: "a nasty error occurring only on gitlab runner slowed down my deployment, in this post I am sharing the solution"
tags: debugging continuosintegration nodejs lambda
canonical_url: https://dev.to/aws-builders/fixing-error-spawnsync-bash-enoent-occurring-during-cdk-lambda-bundling-on-ci-pipeline-1m6i
---

##TLDR;
> if you are struggling with `spawnSync bash ENOENT. at AssetStaging.bundle` Error when bundling a Lambda function check out the `bundling` props of your `CDK NodejsFunction`   and try `forceDockerBundling: true` option. 

Read more for full context and explanation.

---

This week I faced a weird issue that caused the CI/CD pipeline on Gitlab to stop working and I want to share the solution here for future reference. 

###Context
The backend had a Go Backend running on [AWS Fargate](https://aws.amazon.com/fargate/?nc1=h_ls) behind a Load Balancer. 
Deployment happens on Gitlab and stack is written with CDK, therefore the deploy job was running with a `docker:20-dind` (Docker in Docker) image to bundle the binary, build the docker image, upload it to ECR and then use it within a Fargate Task.

Normally in previous project I always used [`sleavely/node-awscli`](https://hub.docker.com/r/sleavely/node-awscli/tags) to build my lambdas and run the CDK deployment but with that go backend and ECS + ECR I had to use [DinD](https://hub.docker.com/_/docker).

Then I added to the stack a Target group pointing to a Lambda written in Node ( following **a strangler pattern approach to refactoring**, that [I described here](https://dev.to/aws-builders/from-php-monolith-to-serverless-multi-stack-cross-account-canary-rollout-njo)).

I deployed from my Mac directly to an ephemeral environment and everything went fine. 
Once I pushed my changes and Gitlab pipeline tried to deploy from the Gitlab runner though, I got a weird error:

```
Error(`Failed to bundle asset ${this.node.path}, bundle output is located at ${bundleErrorDir}: ${err}`); -error: Error: spawnSync bash ENOENT. at AssetStaging.bundle
```

###Investigation

Everything looked fine though, files were there, artifacts were passed along one pipeline job to the other just fine, every dependency was installed properly.

A quick search lead to [StackOverflow](https://stackoverflow.com/questions/73107862/aws-cdk-and-gitlab-returns-error-spawnsync-docker-enoent) but the answers did not help.  I was already using [`esbuild`](https://esbuild.github.io/) and installing it in the dependencies.

Some other searches lead me to [this](https://github.com/aws/aws-cdk/issues/12115) and then finally to this [repo](https://github.com/aws/aws-cdk/blob/v1.78.0/packages/@aws-cdk/aws-lambda-nodejs/README.md#nodejs-function) where I noticed a parameter of the lambda.NodejsFunction Construct that I overlooked:

> `forceDockerBundling?` Type: boolean (optional, default: false)
Force bundling in a Docker container even if local bundling is possible.
This is useful if your function relies on node modules that should be installed (nodeModules) in a Lambda compatible environment.

Honestly I never thought about forcing Docker bundling, why should I do that if [Esbuild](https://esbuild.github.io/) is way more performant?

Anyway, by the end of the morning I had really already tried multiple things with no success, so why not? In the end CDK in the pipeline was using Docker In Docker and Esbuild was not working, so trying to force Docker bundling kinda made sense.

And you know what? It worked of course.

###Solution
Since I did not want to give up my _esbuild_ when bundling locally on my Mac, I looked for [env variables to figure out if I am running on Gitlab](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html) and this is the final working code:

```
 const myLambda = new NodejsFunction(this, `my-lambda`, {
     functionName: `my-lambda`,
     entry: 'lambdas/my-lambda.ts'),
     timeout: Duration.seconds(15),
     handler: 'handler',
     runtime: Runtime.NODEJS_16_X,
     bundling: {
        forceDockerBundling: Boolean(process.env.CI), 
        // use esbuild when running locally otherwise rely on docker
        define: {},
        minify: true,
        externalModules: ['aws-sdk'],
     }
  })
````

Since this caused quite some frustration and headaches during deployment time, I hope it turns out to be useful for others. 

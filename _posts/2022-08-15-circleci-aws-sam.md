---
layout: post
title: "Using CircleCI with AWS SAM"
date: 2022-08-15
description: "I took an hour troubleshooting stuff to find an MVP template for AWS SAM on CircleCI today, here's my results!"
author: "John"
original_slug: circleci-aws-sam
---

## TL;DR - The Code

If all that you do usually is `sam build -u` and `sam deploy --config-env <x>`, this will get you there.

This includes a branch filter to only build prod/dev branches. Don't forget to add your other environments!

This does use the official SAM orb from circleCI, but mostly bypasses the true "integrated" stuff for simplicity.

```yaml
version: 2.1
orbs:
  sam: circleci/aws-sam-serverless@3.2.0
jobs:
  build_and_deploy:
    executor: sam/default
    steps:
      - checkout
      - sam/install
      - run: sam build -u
      - run: sam deploy --config-env ${CIRCLE_BRANCH}

workflows:
  build_and_deploy:
    jobs:
      - build_and_deploy:
          context: "AWS_Secrets"
          filters:
              branches:
                only:
                  - prod
                  - dev
```

Make sure to change your context too! This needs to include environmental variables for your AWS credentials. If you have a context already but use nonstandard environmental variable names, you can enter them like so in the job definition:

```yaml
jobs:
  build_and_deploy:
    executor: sam/default

    steps:
      - checkout
      - sam/install:
          aws-access-key-id: AWS_ACCESS_KEY
          aws-secret-access-key: AWS_SECRET_KEY
          aws-region: AWS_DEFAULT_REGION
      - run: sam build -u
      - run: sam deploy --config-env ${CIRCLE_BRANCH}
```

## A SAM Love Letter

I just don't think there's an easier way to ship code straight to Lambda. (Verbose, normal) cloudformation is a pain in the ass, and CDK never scratched that itch for me. Sam abstracts away a few of the annoying boilerplate things that you have to do to run with cloudformation, and I built an app a while back that used it! Until yesterday, I had survived without CI/CD...

## The CircleCI Orbs Problem

CircleCI has three or so AWS SAM orbs, none of which worked all that well for me. The docs are inconsistent (even on the official orb, damn it!), and commands and such just flat broke sometimes. Thankfully, the official Orb can be trusted to be fairly secure, and we can use it to install SAM and move on with how we'd normally manually deploy the code.

To do this, we'll use the [Official Orb](https://circleci.com/developer/orbs/orb/circleci/aws-sam-serverless) from CircleCI.

## Code, Explained

Defining the main build and deploy job, and checking out the code from git:
```yaml
jobs:
  build_and_deploy:
    executor: sam/default

    steps:
      - checkout
```
This is the breadwinner here, allowing us to just skip using the "automated" stuff in the orb. The three mapping lines are optional, they just tell the orb where the AWS credentials are for the installation process.
```yaml
      - sam/install:
          aws-access-key-id: AWS_ACCESS_KEY
          aws-secret-access-key: AWS_SECRET_KEY
          aws-region: AWS_DEFAULT_REGION
```
Then we run our sam build process like normal, assuming branch names match the config env:
```yaml
      - run: sam build -u
      - run: sam deploy --config-env ${CIRCLE_BRANCH}
```

### Good luck!

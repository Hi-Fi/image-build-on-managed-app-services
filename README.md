# Building container images within non-privileged environment

Managed app environments (Azure Container Apps, Google Cloud Run, AWS Elastic Container Service) are easy way to create dynamic agents/runners for example for GitHub Actions or Azure DevOps. Issue with those ones just is that building of container images is not possible directly as by default Docker requires privileged access to run.

There are many ways tools allowing bypass that limitation. This repository tests some of those with Amazon ECS.

## Prerequisites

- Definition of image that's needed to be built
  - Example one can be found under [`source`](./source) which builds agent that can be utilized with Azure DevOps (code from [Azure-Samples/container-apps-ci-cd-runner-tutorial](https://github.com/Azure-Samples/container-apps-ci-cd-runner-tutorial/blob/main/Dockerfile.azure-pipelines))
- Environment to be used for the run
    - AWS Elastic Container Service Tasks
    - Google Cloud Run Jobs
    - Azure Container App Jobs

## Example steps, AWS ECS

### AWS prerequisites

- ECS cluster created
- ECR repository where produced image is pushed created
- IAM role for ECS task that allows to push produced image to repository (see [ECR](https://github.com/GoogleContainerTools/kaniko?tab=readme-ov-file#pushing-to-amazon-ecr))

### Steps

1. Create task definition
    - Infrastructure requirements
      - Launch type/Task size: used Fargate with quite small CPU/Memory allocation. 
      - Task role: Role with rights to push to target ECR
    - Container 1
      - Image URI: `gcr.io/kaniko-project/executor:latest`
      - Port mappings: remove
      - Environment variables: not used in example
    - Docker configuration
      - Command: Kaniko build arguments
        - Example: `--dockerfile=source/Dockerfile,--context=git://github.com/Hi-Fi/image-build-on-managed-app-services.git,--destination=<aws_account_id.dkr.ecr.region.amazonaws.com/my-repository:my-tag>`
          - Note that git URI can't point to directory, so context is root of the repo (similar to local build from repo root as `docker image build --file source/Dockerfile .`)
2. When task is created, select `Deploy` and `Run task`
    - Target to cluster wanted and adjust compute configuration if needed



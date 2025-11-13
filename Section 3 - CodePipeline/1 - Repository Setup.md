# Repository Setup
— On this step, we will setup all the files needed on your repository and push it to `Github`.

>[!Imporntant]
> On this step, make sure you have already a `Github/GitLab` repository so we will be just pushing changes on your repository.

### Create buildpsec.yaml
In the root level of your repository create `buildspec.yml` that will use on `CodeBuild` to build your `Docker` Image and push it to your ECR Repository. This will also produce the `imageDetail.json` (*for blue/green*) and `imagedenitiions.json` (*for rolling update*) to run your application.

```yaml

version: 0.2
env:
  variables:
    APP_NAME: <app-name> # must change
    CONTAINER_NAME: <app-container-name> # must change

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
      - REGION=${AWS_DEFAULT_REGION}
      - ECR_URI="$ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com"
      - IMAGE_REPO="$ECR_URI/$APP_NAME"
      - IMAGE_TAG=${CODEBUILD_RESOLVED_SOURCE_VERSION:0:8}
      - aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin "$ECR_URI"

  build:
    commands:
      - echo Building Docker image...
      - docker build -t "$IMAGE_REPO:$IMAGE_TAG" -t "$IMAGE_REPO:latest" .

  post_build:
    commands:
      - echo Pushing images to ECR...
      - docker push "$IMAGE_REPO:$IMAGE_TAG"
      - docker push "$IMAGE_REPO:latest"

      - echo Writing imagedefinitions.json...
      - printf '[{"name":"%s","imageUri":"%s"}]\n' "$CONTAINER_NAME" "$IMAGE_REPO:$IMAGE_TAG" > imagedefinitions.json

      - echo Writing imagedefinitions.json...
      - printf '{"ImageURI":"%s"}\n' "$IMAGE_REPO:$IMAGE_TAG" > imageDetail.json

artifacts:
  files:
    - imagedefinitions.json
    - <task-definition-name-from-your-repository>.json # must change
    - appspec.yaml
    - imageDetail.json
```

![](./attachments/Pasted%20image%2020251112155143.png)
### Create appspec.yaml

In the root level of your repository, you also need to have `appspec.yaml`.

```yaml
version: 0.0
Resources:
  - TargetService:
      Type: AWS::ECS::Service
      Properties:
        TaskDefinition: <TASK_DEFINITION> # leave this as is 
        LoadBalancerInfo:
          ContainerName: <app-container-name> # must change
          ContainerPort: <app-port> # must change 
```

![](./attachments/Pasted%20image%2020251112112503.png)

### Update Task Definition
In your task definition from your repository update the `"image"` value to a placeholder you choose, as long as it is enclose with `"<PLACEHOLDER>"`

>[!Note]
>You could freely choose what placeholder you will use.

```json
{
    "image": "<IMAGE_NAME>",
}
```

After you created and updated the necessary file you could now push your change to your repository 🎉.


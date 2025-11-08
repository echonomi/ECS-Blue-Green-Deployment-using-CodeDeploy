
#  ☁️ ECS Blue/Green Deployment with Rolling Update for Staging.
— This project covers the implementation of a Blue/Green (Immutable) deployment strategy for the production environment and a Rolling Update deployment strategy for the staging environment. Throughout this guide, we will use both the AWS Console (click-ops) and the AWS CLI.

#### 📄 This project will be using the following:

```
- Amazon ECS (*Fargate*)
- Amazon ECR
- AWS CodePipeline
- AWS CodeBuild
- AWS CodeDeploy (*Blue/Green for production*)
- Application Load Balancer
- Docker
- GitLab (*as the source repository*)Ï
```

The goal is to implement a **zero-downtime deployment strategy** on `Production` environment with cost-effective **rolling deployment** workflow for `Staging` environment.

#### 📌 High-Level Architecture

![[ecs-blue-green-architecture 3.jpg]]

#### 🚀 Deployment Workflow Summary

| Step | Action                                                              | Service                               |
| :--: | ------------------------------------------------------------------- | ------------------------------------- |
|  1   | Developer pushes code to `main` branch                              | `GitLab`                              |
|  2   | CodePipeline triggers the pipeline                                  | `CodePipeline`                        |
|  3   | Application will be built into a `Docker Image` and pushed to `ECR` | `CodeBuild`                           |
|  4   | Latest `Docker Image` saved into ECR                                | `ECR`                                 |
|  5   | `Staging` environment will be deploy via `rolling update`           | `ECS (Staging Service)`               |
|  6   | Manual approval gating before production                            | `SNS (Email) + CodePipeline Approval` |
|  7   | `Production` deployment will execute                                | `CodeDeploy (Blue/Green)`             |
|  8   | `ALB`shifts traffic from `Blue` —> `Green`                          | `CodeDeploy + ALB`                    |

#### 🎓 Lessons
```
- Blue/Green deployments protect production against bad releases.
- ALB healht checks + CodeDeploy hooks determine smoothness of traffic shift.
- Keeping staging on rolling update reduces cost and simplifies rapid iteration.
- IAM roles and trust relationships are crucial - failure here blocks deployments.
- Loggin & metrics (CloudWatch / ALB metrics) are key for debuging and validation.
```


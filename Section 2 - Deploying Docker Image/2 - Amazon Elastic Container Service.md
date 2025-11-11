# Amazon Elastic Container Service
— In this step, we will use Amazon Elastic Container Service (ECS) to run our application. For this deployment, we’ll be using `Fargate` as the compute option.
Our ECS cluster will contain two services:
- **Staging** service using a Rolling Update deployment strategy.
- **Production** service using a Blue/Green deployment strategy.

### Task Definition
Typically, you would create **two separate task definitions** — one for staging and one for production.  However, for this guide, we will be using **a single task definition** for both environments to keep things simple.

Before you we proceed, we need to ready the **Task Execution Role** and **Task Role**.

**Task Execution Role:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "*"
    }
  ]
}

```

**Task Role**
For my simple `Nest.JS` this won't be needed, but if your application needs to connect on aws services you need to have a task role
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue",
        "dynamodb:PutItem",
        "dynamodb:Query"
      ],
      "Resource": "*"
    }
  ]
}
```

>[!Note]
>This IAM Roles might need to be adjust depending on the needs of your applications.

**Task Definition**
``` json
{
  "family": "<task-definition-family-name>",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "<task-execution-role-arn>",
  "taskRoleArn": "<task-role-arn>",
  "containerDefinitions": [
    {
      "name": "<app-container-name>",
      "image": "<account-id>.dkr.ecr.<region>.amazonaws.com/<ecr-repository-name>:latest",
      "essential": true,
      "portMappings": [
        {
          "containerPort": 3000,
          "protocol": "tcp"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/<app-name>",
          "awslogs-region": "<region>",
          "awslogs-create-group": "true",
          "awslogs-stream-prefix": "ecs"
        }
      }
    }
  ]
}
```

Create your task definition and place it on the root level of your repository
![](./attachments/Pasted%20image%2020251111143958.png)

### Register Task Definition 
After creating your task definition use the following command below to register it in AWS ECS.

```shell
aws ecs register-task-definition --cli-input-json file://<task-definition-filename>.json
```

![](./attachments/Pasted%20image%2020251111144509.png)

### Create ECS Cluster
Next, we need to create an ECS Cluster where our tasks will run. To create a cluster use the command below:

```shell
aws ecs create-cluster --cluster-name <your_cluster_name>
```

![](./attachments/Pasted%20image%2020251111145057.png)

### Create ECS Service
Before you proceed creating service ensure you have the following resources prepared:
- Cluster
- Task Definition
- Subnet ID
- Security Group
- Load Balancer (staging)
- Target Group

Run the command below for staging (Rolling update) ecs service:
```shell
aws ecs create-service \
  --cluster <cluster-name> \
  --service-name <service-name> \
  --task-definition <task-definition-family> \
  --desired-count 1 \
  --launch-type FARGATE \
  --deployment-controller type=CODE_DEPLOY \
  --network-configuration "awsvpcConfiguration={subnets=[<subnet-id>,<subnet-id>],securityGroups=<security-group>,assignPublicIp=DISABLED}" \
  --load-balancers "targetGroupArn=<target-group-arn>,containerName=<app-container>,containerPort=<app-port>"
```

After you run the command you should see the following return output
![](./attachments/Pasted%20image%2020251111151213.png)

After creating service you should see a task running on your ECS cluster.

![](./attachments/Pasted%20image%2020251111153752.png)

You could also test, the DNS provided by your load balancer

![](./attachments/Pasted%20image%2020251111153916.png)

In you production environment,  you can use the same command, but ensure that you have the following resources prepared:
- Cluster (same cluster with your staging)
- Task Definition 
- Subnet ID
- Security Group
- Load Balancer (production)
- Two target group for green and blue

>[!Note]
>You might be encountering IAM Role or permission problem base on your application needs and you task definition requirements (Like creating log group)

thats it you have deploy your application in ECS 🎉.
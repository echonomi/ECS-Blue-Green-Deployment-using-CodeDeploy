
# Amazon Elastic Container Registry
— In this step, we will be using `Elastic Container Registry` to store our `Docker` image in `AWS`.

>[!Note]
>In this step, we will be authenticate our local terminal with **AWS**. Make sure your **AWS** credentials are already configured using either:
>```shell
>aws configure
>```
>or, if your organization use **AWS SSO**:
>```shell
>aws sso login
>```

### Create Registry
First, we need to have a specified registry in AWS to store our `Docker` image. To do that use the command below:
```shell
aws ecr create-repository --repository-name <repository-name>
```

After you created the repository you can check if its created using the command below:

```shell
aws ecr describe-repositories --repository-name <repository-name>
```

![](./attachments/Pasted%20image%2020251111134351.png)

### Login to ECR
To enable you to push a `Docker` image to your ECR repository, you need to log in first.
```shell
aws ecr get-login-password --region <your-region> | docker login --username AWS --password-stdin <aws-account-id>.dkr.ecr.<your-region>.amazonaws.com
```

![](./attachments/Pasted%20image%2020251111135414.png)

### Tag Docker Image
Tag your the `Docker` image you build on your local machine. Use the command below to tag your `Docker` image:
```shell
docker tag <local-docker-image-name>:latest <aws-account-id>.dkr.ecr.<region>.amazonaws.com/<ecr-repository-name>:latest
```

![](./attachments/Pasted%20image%2020251111140325.png)

### Push Docker Image to ECR
After successfully tagged the `Docker image`. you can run the command below to push your image to ECR.
```shell
docker push <docker-you-have-tagged>
```

![](./attachments/Pasted%20image%2020251111140735.png)

thats it, you have pushed your `Docker` image to AWS ECR 🎉.
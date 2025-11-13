
# AWS CodeBuild
— On this step, we will be creating are `CodeBuild`. This will be used to build the `Artifact` needed to deploy our application. For this step we will be using the AWS console.

1. Navigate to **AWS Console** —> **CodeBuild**.
2. Create Project.
3. Add your project name.
4. Choose `Github` or `GitLab` as a source and make a connection.
![](./attachments/Pasted%20image%2020251112114854.png)
>[!Reminder]
>Make sure that you are logged in on your `Github/GitLab` in the same browser you are using so it will hook the right `Github/GitLab` account. Also your repository is allowed to access by AWS.

5. After the connection, select you project repository.
![](./attachments/Pasted%20image%2020251112131936.png)
6. Use the `buildsec.yaml` on your repository as build specifications.
![](./attachments/Pasted%20image%2020251112132127.png)
7. Add S3 as storage to store you artifacts.
>[!Reminder]
>If you couldn't find any or your desired `s3 Bucket` you can create a new one. Just navigate to s3 then create.

![](./attachments/Pasted%20image%2020251112133857.png)
8. By default, the Logs is enable but on this guide we will disable it.
9. Create build project.

Thats it you have create a `CodeBuild` project 🎉
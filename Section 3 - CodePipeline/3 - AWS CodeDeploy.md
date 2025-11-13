# AWS CodeDeploy
— On this step, we will be creating a `CodeDeploy` **production** environment. As the **staging** environment (*Rolling update*) is no longer needed a `CodeDeploy`.

### Create Staging CodeDeploy
1. Navigate to **CodeDeploy** —> **Applications**.
2. Create **Application** & use `Amazon ECS` as a compute platform.
3. Create `Deployment Group` inside your created **application**.
4. Choose your cluster.
5. Choose your production service.
6. Choose your production load balancer.
7. Choose listener port 80 if your load balancer is configure only in HTTP.
8. Choose your **blue** target group as target 1.
9. Choose  your **green** target group as target 2.
10. In Deployment settings, feel free to choose on how will you shift your traffic.
11. In the Original revision termination, feel free to choose on how long will be your window hour for your rollback but in this guide I will make it zero.

>[!Reminder]
>Make sure you have already created a IAM role for `CodeDeploy`. If you have not created an IAM role there is a particular policy for CodeDeploy that you can attach to your IAM role.

After the creation you should be able to have this output

![](./attachments/Pasted%20image%2020251112152955.png)

Thats it you have your `CodeDeploy`. We can now create our `CodePipeline` 🎉

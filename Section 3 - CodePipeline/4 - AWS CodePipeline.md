# AWS CodePipeline
— In this step, we will be creating our `CodePipeline` and connect the `CodeBuild` and `CodeDeploy` by the end of this you should have your own CI/CD pipeline with Blue/Green strategy.

# Create CodePipeline
1. Navigate to **AWS Console** —> **CodePipeline**.
2. Click **Create pipeline** —> **Build custom pipeline**.
3. Enter `pipeline` name and you could leave the other config as is.
![](./attachments/Pasted%20image%2020251112153925.png)
4. Next, Choose `Github/GitLab` as source and choose the same repository that you have choose in `CodeBuild`. Also choose the deployment branch.
![](./attachments/Pasted%20image%2020251112154130.png)
5. In Build State, use other build providers then select `CodeBuild`.
6. Choose the `CodeBuild` Project you have created earlier.
![](./attachments/Pasted%20image%2020251112154308.png)
7. For now let's skip test stage.
8. On the first deploy stage, will be your **staging** environment. Select **Amazon ECS**.
9. Choose your Cluster and staging service.
![](./attachments/Pasted%20image%2020251112154550.png)
10. Create pipeline.
11. After the creation it will deploy your staging environment.
![](./attachments/Pasted%20image%2020251113102737.png)
12. For **production** environment edit the pipeline and add another deploy stage after **staging**.
![](./attachments/Pasted%20image%2020251113103342.png)
13. Add action group, then use ECS (Blue/green) as action provider.
![](./attachments/Pasted%20image%2020251113111244.png)
14. Save changes.
15. Then, click **Release change** to redeploy your pipeline
16. Then, you should be able to successfully deploy your **production**.
![](./attachments/Pasted%20image%2020251113132621.png)

After the deployment you can check your load balancer DNS for **staging** and **production** to see if reflected. You can also try to commit changes on your branch where the pipeline is listening to trigger the pipeline.


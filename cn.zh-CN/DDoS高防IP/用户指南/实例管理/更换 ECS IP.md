# 更换 ECS IP {#task650 .task}

若您的源站IP已暴露，建议您使用阿里云提供的IP，防止黑客绕过高防直接攻击源站。您可以在高防IP管理控制台更换后端ECS的IP，每个账号最多可更换10次。

1.  登录[云盾DDoS防护管理控制台](https://yundun.console.aliyun.com/?p=ddospro)。 
2.  定位到**接入** \> **网站**，单击**更换ECS IP**。 

    **说明：** 更换ECS IP会使您的业务暂时中断几分钟，建议您在操作前先备份好数据。

3.  更换ECS IP需要将ECS停机，若您已将需要更换IP的ECS停机，请直接跳转到步骤4。在更换ECS IP对话框，单击**前往ECS**，在ECS管理控制台将需要更换IP的ECS实例停机。 
    1.  在实例列表中找到目标ECS实例，单击其实例ID。 
    2.  在实例详情页，单击**停止**。 
    3.  选择停止方式，并单击**确定**。 

        **说明：** 停止ECS实例是敏感操作，稳妥起见，需要您输入手机校验码。

    4.  等待ECS实例状态变成**已停止**。 
4.  返回更换ECS IP对话框，输入**ECS实例ID**，并单击**下一步**。 
5.  确认当前ECS实例信息准确无误（尤其是ECS IP）后，选择更换IP后 **是否立刻重启ECS**，并单击**释放IP**。 
6.  成功释放原IP后，单击**下一步**，为该ECS实例重新分配IP。 
7.  ECS IP更换成功，单击**确认**，完成操作。 

    **说明：** 更换IP成功后，请您将新的IP隐藏在高防后面，不要对外暴露。



## CloudWatch vs CloudTrail

- **CloudWath** is monitoring service performance
    - CloudWatch with EC2 will monitor events every **5 minutes** by **default**
    - turn on **detailed monitoring** ⇒ monitor events every **1 minutes**
    - **dashboard**: create dashboard to see what is happening
    - **alarms**: create alarms to notify when particular threshold are hit
    - **envents**: decide how to respond to state changes in AWS resources
    - **logs**: aggregate, monitor, store logs
- **CloudTrail** is auditing (identify which users and accounts called AWS, source IP address, when the calls occurred
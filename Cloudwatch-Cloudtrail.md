## CloudWatch vs CloudTrail

- **CloudWatch** is monitoring service performance
    - CloudWatch with EC2 will monitor events every **5 minutes** by **default**
    - Turn on **detailed monitoring** to monitor events every **1 minutes**
    - **Dashboard**: create Dashboards to see what is happening
    - **Alarms**: create alarms to notify when particular threshold are hit
    - **Events**: decide how to respond to state changes in AWS resources
    - **Logs**: aggregate, monitor, store logs
- **CloudTrail** is auditing which users and accounts called AWS, source IP address, when the calls occurred
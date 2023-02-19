# Week 0 — Billing and Architecture

## Introduction

Cruddur, an ephemeral new micro-blogging platform, allows users to post updates and photos that automatically expire after some time. This ensures a user's personal information stays relevant and in the moment without worrying about any privacy concerns. 

The company has successfully secured funding for demoing the mock web application to investors. Now, it’s time to get Cruddur to the market by making it production ready at scale. 

Cruddur is a microservice-based application. In this bootcamp, we shall run all our services on [AWS](https://aws.amazon.com/?nc2=h_lg). AWS is a cloud service provider. One of our goals in this bootcamp is minimizing the expenses associated with running services on AWS for as low as possible.

To get started with week 0 homework, I'll draw a conceptual and logical diagram to help fully understand the needs of our application.

## Architectural Diagrams
An architectural diagram is a visual representation of all components of a system and the relationship between them.

## Types of Architectural Diagrams
These are a few different kinds of architectural diagrams:

### 1. Conceptual Architecture Diagram: 
It is a pictorial diagram that depicts the relationships of significant components of a system by using blocks, lines or arrows that are easily understood. It is also known as a “Napkin Design.”

![aws3](https://user-images.githubusercontent.com/47522955/219877042-70ad48c8-753a-4ceb-bac5-3d0e843904e7.jpg)

### 2. Logical Architecture Diagram: 
In software development, a logical architecture diagram guides the research and development of software. The purpose of a logical architecture diagram is to describe the functionality and behaviour of the future system as it should operate while in service.

![Screenshot_20230219_064915](https://user-images.githubusercontent.com/47522955/219965714-24463e11-576e-4556-a1c4-ce84a2f1f3a1.png)

My Lucid Chart is available [here](https://lucid.app/lucidchart/a970bd13-b83a-4fb2-8de5-95a3d0120701/edit?viewport_loc=-1506%2C-1046%2C5980%2C2304%2C0_0&invitationId=inv_39423882-5909-43be-9d91-ef6153bb8ae1)

### 3. Physical Architecture Diagram:
this is a physical layout of the system and its components. 
                      
```It is needful to mention that as a prerequisite for this Bootcamp, I had set up an AWS IAM user, multifactor authentication (MFA), assigned the IAM user a role, granted the IAM user access to billing and cost console and, also set up a [Gitpod account](https://www.gitpod.io/).```

## AWS Billing Alerts
AWS Billing Alerts is a way of monitoring your estimated charges for your AWS account. How to do this is by making enabling Billing Alarms in your Root User or IAM user that has permission to view billing information. This alarm only gets triggered when your account billing exceeds your threshold. A threshold is a specified percentage of your billing cost set to alert when exceeded. US East (N. Virginia) Region represents AWS global charges.

I logged into the  [AWS Billing Console](https://us-east-1.console.aws.amazon.com/billing/home?region=us-east-1#/bills?year=2023&month=2) as a Root user to set up Billing Alerts.

- Under ```Billing Preferences```, I choose ```Receive PDF Invoice By Email``` > ```Receive Free Tier Usage Alerts``` 
- Provide my email address
- Check the ```Receive Billing Alerts```
- ```Save Preferences```

![aws bootcamp 1](https://user-images.githubusercontent.com/47522955/219872468-a6b231e4-d996-449e-8d80-3b889a7d232d.png)

## Create a Billing Alarm on CloudWatch
CloudWatch is an Amazon service that collects, monitors, and graphically displays all your logs, event and metric data. 

To create a Billing Alarm I opened the [CloudWatch Console](https://console.aws.amazon.com/cloudwatch/)

- Under ```Alarms``` Choose ```All Alarms```
- `Create alarm `>  `Metric` > `Billing` > `Service`
- Under `Total Estimated Charges` Choose `USD`
- Select `Metric`
Next, I named the billing alarm as "AWS Bootcamp Billing Alert" and set the Threshold value to $10. 
See [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html) to complete the steps.


Furthermore, AWS charges $0.0 for the first 10 alarm metrics, after which you may pay up to $0.30 per instance per month. See the [CloudWatch Pricing Documentation](https://aws.amazon.com/cloudwatch/pricing/) 

![AWS3](https://user-images.githubusercontent.com/47522955/219966160-405cbac8-df8c-4459-a162-14c27115c70f.png)

## Create an AWS Budget

With AWS Budget, you can set up a custom budget to monitor your spending and get alerts if your actual cost exceeds your budgeted cost. An actual cost refers to the real, current cost of using AWS services, as reported by AWS. It includes all the charges incurred for using the services, such as instance usage, storage, data transfer, and other applicable fees. 
Budgeted cost refers to the estimated cost that you have set for using AWS services for a given period to monitor your spending and avoid any unexpected expenses.

I created two Budgets. One for actual dollar spend and the other for Credit spend.

You can achieved this using either AWS CLI or the console. I practised both methods and gained familiarity with each. The "Example Tag Budget" was created using AWS CLI, while the "My Zero-Spend Budget" was created using the AWS Console.

AWS provides two free budgets for Free tier accounts, but if you surpass them, you will incur charges.

![aws4](https://user-images.githubusercontent.com/47522955/219975800-6d3fb89a-cca9-40dd-8fd5-e6477189733e.png)





# Week 0 — Billing and Architecture

## Introduction

Cruddur is an ephemeral new micro-blogging platform that allows users to post updates, and photos that automatically expire after a period of time, ensuring that your personal information stays relevant and in the moment without worrying about any privacy concerns. 

The company has successfully secured funding through demoing the mock web application to investors and now, It’s time to get Cruddur to the market or make it production ready at scale and that’s what we shall do in this Bootcamp.

Cruddur is a Microservice-based application and we shall use [AWS](https://aws.amazon.com/?nc2=h_lg) as our cloud service provider. One of the company’s goals is to keep the cost of running services on AWS as low as possible.

To get started with this week’s task, I’m going to create a conceptual and logical diagram.

## Architectural Diagrams
An architectural diagram is a visual representation of all components of a system and the relationship between them.

## Types of Architectural Diagrams
Following are some types of architectural diagrams:

### 1. Conceptual Architecture Diagram: 
It is a pictorial diagram that depicts the relationships of significant components of a system by using blocks, lines or arrows that are easily understood. It is also known as a “Napkin Design”.

![aws3](https://user-images.githubusercontent.com/47522955/219877042-70ad48c8-753a-4ceb-bac5-3d0e843904e7.jpg)

### 2. Logical Architecture Diagram: 
In software development, a logical architecture diagram guides the research and development of software. The main purpose of the logical architecture diagram is to provide a description of the functionality and behaviour of the future system as it should operate while in service.

![AWS 2](https://user-images.githubusercontent.com/47522955/219875904-b4b7ce2c-62c0-419a-a1aa-ab72ab62ce35.png)

My Lucid Chart is available [here](https://lucid.app/lucidchart/a970bd13-b83a-4fb2-8de5-95a3d0120701/edit?view_items=tCnym.H0SUOy&invitationId=inv_39423882-5909-43be-9d91-ef6153bb8ae)

### 3. Physical Architecture Diagram:
this is a physical layout of the system and its components. 
                      
```It is important to mention that as a prerequisite for this Bootcamp, I’d set up my AWS IAM user, multifactor authenticator (MFA), and IAM role, granted IAM user access to billing and cost console and, set up a Gitpod account.```

## AWS Billing Alerts
To track my AWS usage charges, I turned on Billing Alerts to monitor my AWS usage charges and recurring fees automatically.
- On your ```Root Account```, go to the ```Billing Page```
- Under ```Billing Preferences```, I choose ```Receive PDF Invoice By Email``` > ```Receive Free Tier Usage Alerts``` 
- Provided my  email address
- Check the ```Receive Billing Alerts```
```Save Preferences```

![aws bootcamp 1](https://user-images.githubusercontent.com/47522955/219872468-a6b231e4-d996-449e-8d80-3b889a7d232d.png)

## Manage Billing Alerts
- Go to ```CloudWatch```
- Under ```Alarms``` Choose ```All Alarms```
- `Create alarm `>  `Metric` > `Billing` > `Service`
- Under `Total Estimated Charges` Choose `USD`
- Select `Metric`                   
After I selected metric, I give it a name e.g, AWS Bootcamp Billing Alert. Then, define the Threshold value to $10. 

## Configure Actions
In the next step, I was directed to the ```Configure Actions``` page
- Under ```Alarm``` Select the second box
- Create ```New Topic```
- Add Your Email Address
- Create Topic
- Next

## Name and Description
This is the final step after clicking on ```Next```
Create ```Alarm name``` > Next > ```Create Alarm```
One more thing to mention is that AWS charges $0.00 per alarm metric for the first 10 alarm metrics after which you’ll be charged some cents.


## Create an AWS Budget
Here, I’m going to create AWS Budget. We can either create our budget in AWS or use ```AWS CLI```. For this, I created the Budget in AWS. For a Free tier account, AWS offers 2 free budgets. If exceeded you’ll get charged.


# Section-10-Deploying-Sample-App-With-CRUD-Functionality-on-AWS-Without-CI-CD-


# Push CRUD App on CodeCommit & S3 - Lab

**Step 1.Goto Visual Studio code**
- Run the following commands
```sh
$ cd crud-app/
$ git init
$ touch .gitignore 
# .gitignore files are - .env.dev,node_modules/,.DS_Store
```
**Step 2.Goto AWS Console>Developer Tools>CodeCommit>Repositories>Create Repository**
In repsitory settings-
- Repository name - crud-app

Click on Create

**Step 3.Goto AWS Console>CodeCommit>Repositories>crud-app**
- Click to copy on Clone URL>Clone HTTPS

**Step 4.Go back to Step 1 and type following commands**
```sh
$ git remote add origin https://git-commit.ap.south-1.amazonaws.com/v1/repos/crud-app**
$ cat .git/config 
# see entry in config file
$ git status
$ git add .gitignore
$ git commit -m "first commit"
$ git push -u origin master
# To Verify above goto AWS Console>CodeCommit>Repositories>crud-app>See the entry for .gitiginore
$ git add .
$ git status
$ git commit -m "pushing all other files and folders"
$ git push
$ rm -rf node_modules/
$ rm -f package-lock.json
$zip -r Nodejs-curd-app-with-dynamodb.zip . -x "_MACOSX" -x ".DS_Store" -x ".env.dev"
```

**Step 5.Goto AWS Console>S3>Buckets>Create Bucket**
- Bucket name - nodejs-curd-app-with-dynamodb-amit
- Region - Asia Pacific(Mumbai)ap-south-1
- Keep all other settings default 

Click on Create Bucket

**Step 6.Amazon S3>Buckets>nodejs-curd-app-with-dynamodb-amit>Click on Upload>Add files**

- Choose Nodejs-curd-app-with-dynamodb.zip>Open>Upload
- Nodejs-curd-app-with-dynamodb.zip is uploaded to S3

# End of Lab

# Deploy CRUD App on Single EC2 - Lab

**Step 1.Launch an Instance with following settings:**

Keep all the things default besides the following:
- IAM role - Ec2S3FullAccess
- Security Group - 80,22,3000 Port open 

**Step 2.Connect the Instance and type following commands**
```sh
$ sudo su 
$ yum update -y
$ curl -sL https://rpm.nodesource.com/setup_lts.x | bash - 
$ yum install nodejs -y
$ npm install -g pm2
$ pm2 update
$ aws s3 cp s3://nodejs-curd-app-with-dynamodb-amit/Nodejs-curd-app-with-.zip .
$ unzip Nodejs-curd-app-with-dynamodb.zip
$ ls -a
$ npm install
$ npm start
```
**Step 3.Copy Public ip of the instance and paste it in browser**
- e.g 13.122.218.51:3000

**Step 4.Goto Postman Tool and replace localhost:3000 with following**
- http://Public-Ip:3000/createTable
- Click on Send
  - Error:need dynamoDB access

**Step 5. Goto IAM>Roles>Ec2S3FullAccess>Attach Policy**
- Select AmazonDynamoDBFullAccess policy
- Click to attach policy

**Step 6.Go back to Step 4 and click on Send**
- Now Successful

**Step 7.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 8.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/readData
- Click on Send

**Step 9.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/insertData
- Click on Send

**Step 10.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- Check for the data inserted

**Step 11.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/updateData
- Click on Send

**Step 12.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 13.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/deleteTable
- Click on Send

**Step 14.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

# End of Lab


# Manage Postman Environments - Lab

**Step 1.Open Postman Tool**
- Click on Top Right corner Manage Environments>Add
- In Add Environment - Name as "dev"
  - Variable - url
  - Initial Value - http://localhost:3000
  - Current Value - http://localhost:3000
  - Click on ADD and close this window

**Step 2.Open Postman Tool**
- Click on Top Right corner Manage Environments>Add
- In Add Environment - Name as "ec2"
  - Variable - url
  - Initial Value - http://13.126.211.32:3000
  - Current Value - http://13.126.211.32:3000
  - Click on ADD and close this window

**Step 3.Click on EC2**
- Provide environment value as - {{url}}

**Step 4.Now select {{url}}/create table and Click on Send**

**Step 5.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 6.Goto Postman Tool and put the following value**
- http://{{url}}/readData
- Click on Send

**Step 7.Goto Postman Tool and put the following value**
- http://{{url}}/insertData
- Click on Send

**Step 8.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- New Item added successfully

**Step 9.Goto Postman Tool and put the following value**
- http://{{url}}/updateData
- Click on Send

**Step 10.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 11.Goto Postman Tool and put the following value**
- http://{{url}}/deleteData
- Click on Send

**Step 12.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data deleted

**Step 13.Goto Postman Tool and put the following value**
- http://{{url}}/deleteTable
- Click on Send

**Step 14.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

**Step 15.Goto Visual Studio code**
- Run the following commands
```sh
# "start":"./node_modules/.bin/env-cmd -f ./.env.dev pm2 start -f app.js"
$ npm install
$ npm start
$ ./node_modules/.bin/env-cmd -f ./.env.dev node app.js
# server is up on port 3000
```
**Step 16.Open browser and type localhost:3000**
- Application is running

**Step 17.Goto Postman Tool and change environment to dev**
- Put the following value
  - http://{{url}}/updateData
  - Click on Send

Click on Send

**Step 18.Goto Postman Tool and put the following value**
- http://{{url}}/deleteData
- Click on Send

**Step 19.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data deleted

**Step 20.Goto Postman Tool and change environment to ec2**
- Put the following value
  - http://{{url}}/deleteTable
  - Click on Send

**Step 21.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

# End of lab


# Deploy CRUD App on EC2 Fleet with Auto Scaling- Lab

**Step 1. Goto AWS Management Console>Services>Ec2>Ec2Dashboard>Auto Scaling>Launch Configurations**

**Step 2. Click on Create Launch configuration**

Provide the following details:
- Give name - lc-cd-crud
- AMI - Copy AMI-Id from launch Instance 
- Instance type- choose t2.micro
- IAM Instance profile - EC2S3FullAccess

**Step 3. Click on Advanced details>user data>As text**
- Type the following commands-
```sh
#!/bin/bash
$ yum update -y
$ yum install nmap-ncat -y
$ curl -sL https://rpm.nodesource.com/setup_lts.x | bash -
$ yum install nodejs -y
$ npm install -g pm2
$ pm2 update
$ cd /home/ec2-user
$ aws s3 cp s3://nodejs-curd-app-with-dynamodb-amit/Nodejs-curd-app-with-dynamodb.zip
$ unzip new-node-project
$ npm install
$ npm run start
$ sleep 10
$ INSTANCE_ID=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
$ aws autoscaling complete-lifecycle-action --lifecycle-action-result CONTINUE --instance-id
$ $INSTANCE_ID --lifecycle-hook-name test-hook--auto-scaling-group-name test-asg --region ap-south-1 || \
$ aws autoscaling complete-lifecycle-action --lifecycle-action-result ABANDON --instance-id
$ $INSTANCE_ID --lifecycle-hook-name test-hook --auto-scaling-group-name test-asg --region ap-south-1
```
**Step 4. Select Security Group and key pair**

**Step 5. Click Create Launch Configurations**
- Successfully created launch configuration test-lc

**Step 6. Select lc-cd-crud >Actions>Create Auto Scaling group**
- Give Auto Scaling group name - test-asg

Click on Next

**Step 7.Select Vpc and select all subnets**
- Click on Next

**Step 8.Configure advanced options**
- Load Balancing - No load Balancer
- Health checks - Select ELB 
  - Health check grace period - 120 seconds

Click on Next

**Step 9.Group size**
- Desired capacity - 1
- Minimum capacity - 1
- Maximum capacity - 1

Scaling policies - None

Click on Next

**Step 10.Add Notifications**
Click on Next

**Step 11.Add Tags**
Click on Next

**Step 12. Review and Click on Create Auto Scaling group**

**Step 13.Ec2>Auto Scaling group>Instance Management>Create lifecycle hook**
- Lifecycle hook name - test-hook
- Lifecycle transition - Instance launch
- Heartbeat timeout - 300 seconds
- Default result - ABANDON

Click on Create

**Step 14.Ec2>Auto Scaling groups>test-asg>Edit Group size**
- Desired - 1
- Minimum - 1
- Maximum - 1

Click on Update

**Step 15.Ec2>Auto Scaling Group>asg-cd>Instances**
- See the lifecycle behaviour Pending>Pending-wait>Pending-Wait:Proceed>InService

**Step 16.Click Instance and copy & paste the Public IP address in browser to see it running**
- e.g.- 13.222.125.52:3000

**Step 17.Open Postman Tool>Environment>Ec2**
- In Ec2 change current value of ec2 url - 13.222.125.52:3000
- Click on Update

**Step 18.Now select {{url}}/create table and Click on Send**

**Step 19.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 20.Goto Postman Tool and put the following value**
- http://{{url}}/readData
- Click on Send

**Step 21.Goto Postman Tool and put the following value**
- http://{{url}}/insertData
- Click on Send

**Step 22.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- Check for the data inserted

**Step 23.Goto Postman Tool and put the following value**
- http://{{url}}/updateData
- Click on Send

**Step 24.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 25.Goto Postman Tool and put the following value**
- http://{{url}}/deleteData
- Click on Send

**Step 26.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data deleted

**Step 27.Goto Postman Tool and put the following value**
- http://{{url}}/deleteTable
- Click on Send

**Step 28.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

# End of Lab



# Deploy CRUD App on EC2 Fleet with ALB : Part 1 - Lab

**Step 1.Goto Ec2>Auto Scaling groups>test-asg>Edit**
- Edit test-asg - 
- Desired - 3
- Minimum - 3
- Maximum - 3

- Health checks
  - Select ELB
  - Health check grace period - 120 seconds

Click on Update

**Step 2.Goto Ec2>Target Groups>Create Target Group**
In Basic configuration:-
- Choose Target - Select Instances
- Target group name - "tg-crud"
- Protcol,Port - HTTP,3000
- Everything else is same

Click on Next

**Step 3.Register targets**
- Select all 3 instances created by "test-asg"
- Click on Include as pending below

Click on Create target group

**Step 4.Goto AWS Console>All services>EC2>Load Balancing>load balancers>create load balancer**
- Select Application Load Balancer
- In Configure load balancer
  - Name - alb-crud
  - Scheme-internet facing
  - IP address type-ipv4
  - Listeners
    - Protcol - HTTP
	- Port    - 80
- Availability zones 
  - select vpc as default 
  - select all 3 AZs

Click on Configure security settings

**Step 5.Click on Configure security groups**
- Select security group

Click on Next:Configure routing

**Step 6.In Configure routing**
- Target group - Existing target group
- Name - tg-crud
- Everything as same

Click Next:Register targets

**Step 7.Click on Next:review**

**Step 8.Click on Create**

**Step 9.Click on Close**

**Step 10.Goto Ec2Dashboard>Load balancers>alb-crud**
- It is in provisioning state

**Step 8.Goto Ec2Dashboard>Auto Scaling>Auto scaling groups>test-asg**
- Load balancing>load balancers
  - Select Application Load Balancer
    - also select target group as tg-crud |HTTP
  
Click on Update

**Step 11.Goto Ec2>Target groups>tg-crud**
- See that instances are Healthy now

**Step 12.Goto Ec2Dashboard>Load balancers>alb-crud**
- It is in active state

**Step 13.Copy DNS name of alb-crud**
- Paste it in the Browser and see that Application is Running
- ALB is passing traffic equally to all instances

**Step 14.Open Postman Tool**
- Click on Top Right corner Manage Environments
- In Add Environment - Name as "ALB"
  - Variable - url
  - Initial Value - DNS name of ALB e.g-http://alb-crud-xxxxx.ap.south-1.elb.amazonaws.com
  - Click on ADD and close this window

- Now change the Environment to ALB 

**Step 15.Now select {{url}}/create table and Click on Send**

**Step 16.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 17.Goto Postman Tool and put the following value**
- http://{{url}}/readData
- Click on Send

**Step 18.Goto Postman Tool and put the following value**
- http://{{url}}/insertData
- Click on Send

**Step 19.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- New Item added successfully

**Step 20.Goto Postman Tool and put the following value**
- http://{{url}}/updateData
- Click on Send

**Step 21.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 22.Goto Postman Tool and put the following value**
- http://{{url}}/deleteData
- Click on Send

**Step 23.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data deleted

**Step 24.Goto Postman Tool and put the following value**
- http://{{url}}/deleteTable
- Click on Send

**Step 25.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

**Step 26.Ec2 Dashboard>Security Groups>sg-xxxx>Edit Inbound rules**
- In Custom TCP | 3000 - Select Source as "Security group of ALB"
- Now Traffic will be served only to ALB's DNS only and not for individual instance

# End of Lab

# Deploy CRUD App on Elastic Beanstalk : Part 1 - Lab

**Step 1.AWS Console>Services>Elastic Beanstalk>Create Application**
- Create a web app
  - Application Name- crud-app
  - Platform 
    - Platform 
      - Node.js
   - Platform branch
      - Node.js 12 running on 64bit Amazon Llinux2
   - Platform version
      - 5.2.3(Recommended)
   - Application Code - Local file>Choose file

- Click on Configure more options
  - In Presets select High availability
  - Click on Save

Click on Create App

**Step 2. Creating environment started**
- Elastic BeanStalk launches an environment named CrudApp-env
- Click on CrudApp-env to check the environment

**Step 3. Goto Ec2Dashboard>CrudApp-env Instance>Actions>Connect>Connect**
Run the following commands:
```sh
$ cd /var/app/current
$ ls -a
$ cat Procfile
$ cd /var/log
$ ls
$ cat eb-engine.log
```
**Step 4.Goto Ec2Dashboard>CrudApp-env>Security>sg-xxxxxx>Edit Inbound rules**

- Port 80 and 22 are already open
- Click on Add rule for port 3000 from anywhere 

**Step 5. Paste CrudApp-env'sPublic Ip:3000 in browser**
- See that App is running

**Step 6. Goto Ec2>Target groups>awseb-xxx>Group details**
- Port open for 80

# End of lab


# Deploy CRUD App on Elastic Beanstalk : Part 2 - Lab

**Step 1.Open Visual Studio Code**
- Open .env.prod and edit as following
```sh
$ DEPLOYMENT_ENV = prod
$ AWS_REGION = ap-south-1
$ PORT = 8080
$ ENDPOINT = http://dynamodb.ap-south-1.amazonaws.com
```
**Step 2.Run the following commands**
```sh
$ touch Procfile
# Procfile - web: ./node_modules/.bin/env-cmd -f ./.env.prod node app.js
$ zip -r Nodejs-curd-app-with-dynamodb.zip . -x "_MACOSX" -x ".DS_Store" -x ".env.dev"
```
**Step 3.Goto Elastic Beanstalk>Applications>crud-app>Actions>Delete Application>Confirm>Delete**

**Step 4.Goto AWS Console>Services>Elastic Beanstalk>Create Application**
- Give Application name - crud-app-1
- In Platform - 
  - Platform - nodejs
  - Platform branch - Node.js 12 running on 64 bit Amazon linux 2
  - Platform version - 5.2.3(Recommended)
- In Application code -
  - Upload code>Local file>choose file>upload

- Click on Configure more options
  - Presets - Select High availability
  - Select security>Ec2Keypair>teacheramitkkp
  - Keep rest as it is
  
- Click on Save

Click on create app

**Step 5.Click on crud-app1-env Link**
- See Application is running

**Step 6.Goto Ec2Dashboard>crud-env>Connect>connect**

Run the following commands -
```sh
$ cd /var/app/current
$ ls -a
$ cat Procfile
$ cat eb-engine.log
$ ls
```
**Step 7.Open Postman Tool**
- Click on Top Right corner Manage Environments
- In Add Environment - Name as "ebs"
  - Variable - url
  - Initial Value - http://crudapp1-env.eba-ysepumd.ap.south-1.elasticbeanstalk,com
  - Click on ADD and close this window

**Step 8.Open Postman Tool>Environment**
- Change environment to "ebs"

**Step 9.Now select {{url}}/create table and Click on Send**
- Error:Not authorized to perform

**Step 10.Goto AWS Console>Services>IAM>Roles>aws-elasticbeanstalk-ec2-role**
- Click on this role>Permissions>Attach policy
  - Select AmazonDynamoDBFullAccess
- Click on Attach Policy

**Step 11.Now Open Postman Tool and select {{url}}/create table and Click on Send**
- Table created successfully

**Step 12.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 13.Goto Postman Tool and put the following value**
- http://{{url}}/readData
- Click on Send

**Step 14.Goto Postman Tool and put the following value**
- http://{{url}}/insertData
- Click on Send

**Step 15.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- New Item added successfully

**Step 16.Goto Postman Tool and put the following value**
- http://{{url}}/updateData
- Click on Send

**Step 17.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 18.Goto Postman Tool and put the following value**
- http://{{url}}/deleteData
- Click on Send

**Step 19.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data deleted

**Step 20.Goto Postman Tool and put the following value**
- http://{{url}}/deleteTable
- Click on Send

**Step 21.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

# End of Lab

# Section Clean-up - Lab

**Step 1. Goto AWS Management Console>EC2 Dashboard>Auto Scaling Groups>test-asg>delete>confirm delete**

**Step 2. AWS Management Console>Services>ElasticBeanStalk>Applications>crud-app-1>Actions>Delete Application**
- Enter the name in Confirm Application Deletion
- Click on Delete

**Step 3. AWS Management Console>EC2 Dashboard>Load Balancing>load Balancers>alb-crud>Actions>Delete**
- Click on Yes,Delete

**Step 4. AWS Management Console>EC2 Dashboard>Load Balancing>Target groups>tg-crud>Actions>Delete**
- Click on Yes,Delete 

# End of lab










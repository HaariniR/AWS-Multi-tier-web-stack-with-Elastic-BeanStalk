# AWS-Multi-tier-web-stack-with-Elastic-BeanStalk
Provisioning a multi tier web application stack with elastic beanstalk
Objective of this project is to use PAAS & SAAS offered by AWS to provision a highly available jave web application stack

# Problem:
Setting up a complete stack on ec2 instances will have the following problems
-- Overhead in maintaing minor version upgrade or patching/updates of the services
-- Manual process of provisoning and installing dependencies on each ec2 instance
-- This is not suitable for large scale implementation of web application with huge traffic

# Solution:
Utilising the PAAS & SAAS services offered by AWS.
-- By using Elastic Beanstalk we were able to provision ec2 instances, load balancer, ASG with few clicks. So, it completely saved us from provisioning individual ec2 instances, installing dependencies of tomcat, removing default application and then loading the artifact, etc.
-- As all the services in this setup are aws managed we don't need to worry about minor version upgrade or patching/updates on this services.
-- There is rolling deployment policy that provides deployment with minimal downtime

# AWS services involed:
1) Cloudfront
2) Elastic BeanStalk
     -- Tomcat with EC2 instances deployed by beanstalk
     -- Application load balancer along with auto scaling group that will automate ec2 instance scaling
3) RDS - This will be the MYSQL database server
4) ActiveMQ
5) ElasticCache

# Steps Involved:
1) Create Key pairs and security group for backend services.
2) Provision Multi-AZ MySQL using RDS service in private subnet group of our custom VPC.
3) Provision 2xnode Memcached cluster using Elastic Cache in private subnet group of our custom VPC.
4) Provision Active/Standby ActiveMQ message broker using Amazon MQ service in 2xprivate subnets our custom VPC.
5) Upload build application artifcat in Elastic Beanstalk, which in turns deploys our complete web application by provisioning tomcat ec2 instances in Auto Scaling     Group(ASG), ALB, etc. and then update backend security group to allow frontend to interact with it.
6) Then Copy our Elastic Beanstalk environment end point and add it as CNAME in the domain provider dns setting to divert the traffic to ALB, So, that users can use meaningful URL to reach our application. And update the same URL in Cloud Front to be distributed using CDN.

# Screenshots:

Step 1: Security group for the backend services
![image](https://user-images.githubusercontent.com/102613218/221905420-b5f1ec3e-fc8b-4207-91ac-c1b43d64fc45.png)
Allowing internal traffic among services, also allowing traffic from tomcat instance that is launched by elastic load balancer

Step 2: Provision Multi-AZ MySQL using RDS service in private subnet group of our custom VPC
![image](https://user-images.githubusercontent.com/10261321![image])
8/221905827-1e9afaf7-0a3b-441e-bb2b-83c63b1bf78d.png)
![image](https://user-images.githubusercontent.com/102613218/221905878-15359edb-4dd2-4c7c-a086-ed533f044f30.png)

Creating parameter group, subnet group, username & password to deploy the RDS
Database is initialised by launching a ec2 instance, installing mysql client & connecting to the rds endpoint.

Step 3: Provision 2xnode Memcached cluster using Elastic Cache in private subnet group of our custom VPC
![image](https://user-images.githubusercontent.com/102613218/221907420-2ea14098-d926-4eaa-bd5c-92640432dace.png)
![image](https://user-images.githubusercontent.com/102613218/221907477-6496d4f1-2c12-40f1-ba40-0165a052f099.png)

Step 4: Provision Active/Standby ActiveMQ message broker using Amazon MQ service in 2xprivate subnets our custom VPC
![image](https://user-images.githubusercontent.com/102613218/221908056-ddd541a7-091d-4426-a4db-99576b5bcdb6.png)
![image](https://user-images.githubusercontent.com/102613218/221908109-af6609bf-638a-401f-915c-428f73c0c9fd.png)

All the backend services are implemented with the backend security group created earlier

Step 5: Deploying application using elastic beanstalk
Upload build application artifcat in Elastic Beanstalk, which in turns deploys our complete web application by provisioning tomcat ec2 instances in Auto Scaling     Group(ASG), ALB, etc. and then update backend security group to allow frontend to interact with it
![image](https://user-images.githubusercontent.com/102613218/221908749-91a27a19-e7fa-49fd-8947-1d6e619a91d3.png)
![image](https://user-images.githubusercontent.com/102613218/221908851-d679480d-60b3-4ef1-a712-5e3816833621.png)
![image](https://user-images.githubusercontent.com/102613218/221908891-625fed87-48f4-4054-8ace-b015e8efe506.png)
![image](https://user-images.githubusercontent.com/102613218/221908917-6d3db469-f0bd-4c2d-af22-6f412aace7a0.png)
![image](https://user-images.githubusercontent.com/102613218/221908964-970db1b4-eb6f-4414-b85a-b9dc3645df32.png)

Step 6: Setting up cloudfront for global distribution
Once the application is up & running the elastic beanstalk endpoint is made to point to CNAME of our domain and also Cloudfront with configured with our domain
SSL certificate is specified to allow both HTTPS & HTTP connection.
All edge locations are selected to have higher performance
![image](https://user-images.githubusercontent.com/102613218/221910460-d9c69794-3901-4d23-a697-8c4aaa8d1ab4.png
![image](https://user-images.githubusercontent.com/102613218/221910511-87e5d790-d629-47c4-a8ff-793df8c5ec83.png)






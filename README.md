# AWS Load Balancer

PROJECT AIM : To understand the conecpt of high availability and scalability in cloud computing by taking practical steps in setting up an application load balancers and configuring auto scaling groups in AWS. 

This project will we divided into 2 stages, they are;
1. Stage 1: Setting up Application Load Balancer.

2. Stage 2: Configuring the Auto Scaling Group.


> # Stage 1 - Application Load Balancer

Application Load Balancer (ALB) is a type of load balancer service that distributes incoming application traffic across multiple targets, such as EC2 instances, containers, and IP addresses, in multiple Availability Zones. 

It operates at the application layer (Layer 7) of the OSI model and supports advanced routing, SSL termination, and web application firewall integration.

> ## Create EC2 Insatnce

- Create 2 EC2 instances, The first instance will have the the configuration below.

![EC2](./Img/1A.%20EC2%20CONFIGURATION.png)

- A new security group is created, ssh and http inbound rule is added to the security group.

![EC2](./img/1b,%20ec2%20sg.png)

- The same configuration was done for the second instance. now we have 2 instances running for this project.


![EC2](./img/4.%20INSTANCES.png)

> ## Test 1

Firstly, we are going to test our instances by Checking if our public IPv4 addresses has access to the web server.
The public IP address for the first and second ec2 instance is copied and added to a web browser. The result shows our instance has access to the internet.

## First instance 
![EC2](./img/5.%20FIRST%20TEST.png)

## Second Instance 
![EC2](./img/6.png)


>## Create An Appliction Load Balancer

Navigate to *Load Balancers* option under the Ec2 menu, select *create Load balancer*, and select *application Load balancer* as we are using HTTP for web pages.

![LB](./Img/7.%20Load%20balancer.png)

### Configuration for the Load balancer

- The load balancer is named '*my-first-ALB*', 

- Scheme is '*Internet-facing*',

- IP address type is '*IPv4*'

- All zones are selected for the availablilty Zone.

- A new security group is created for the Load balancer, this is named '*ALB-demo-SG*'. This security group will allow HTTP from everywhere.

- HTTP *Anywhere IPv4* is added as an inbound rule for the security group.


![SG](./IMG/8.%20SG.png)

- Listening port is going to listen on port 80 as that is where the HTTP traffic comes from. This will then be transferes to a target group which includes the 2 instances that were previously created. 




> ## Creating Traget Group

Luckily for us, AWS has made it easy to create a target group just as you are configuring your Load balancer. 

- The Target group will be named '*my-ALB-TG*

- The 2 instances will be selected and '*incuded as pending below*'

![TG](./IMG/9.%20TG.png)



- Create Traget group and attach to Load balancer.

![TG](./IMG/10.%20listener.png)




- In summary, we have created an Application Load Balancer that is internet-facing, we are using the ALB-demo-SG as our security group which has port 80 open for HTTP traffic. We are using 3 different availabiltiy zones in the VPC so it is highly available, and we are listening for traffic on port 80 which would then route to our target group which has 2 instances in it.


![LB](./IMG/11.%20SUMMARY.png)


> ## Test 2

For the second testing, we will be copying the DNS name in the details section of our load balancer and running it on chrome.

![DNS](./IMG/12.%20DNS.png)



The first result has an Ip-172-31-22-75, when this page is refreshed, the IP changes to the second host with address 172-31-32-12.
If you keep refershing, you will notice the traffic toggles between the 2 hosts, this means that traffic is being routed between these 2 instances by the load balancer.
This is how we know our instances are healthy.

![IP](./img/13.%20RESULT%201.png)

![IP](./Img/14.%20RESULT%202.png)

![IP](./ImG/15.%20healthy.png)


> # Stage 2 - Auto Scaling Groups 

Auto scalling groups automatically scale out and in instances based on the load. All we need to do is to specify instance numbers, that is the desird capacity, minimum and maximum.
As new instances come online, they automatically get registered with our load balancer and it replaces unhealthy instances automatically.

Essentially this allows usnto run at an optimal capacity, meaning saving costs.

>## Creating an Auto scaling group

 To begin, we will be removing the 2 instances that was previously created from our tartget group. this ensures our target group is now empty.
On our EC2 menu, we will navigate to the Auto Scaling Group option and select '*create Auto Scaling Group*'

![ASG](./IMG/16.%20asg.png)

## configuration

- Auto Scaling Group is named '*my-first-ASG*'

- For template, we are creating a lunch template.

![ASG](./IMG/17..png)

- The name for our lunch template is '*my-first-launch-temp*'.

- Application and OS Images (Amazon Machine Image) is selected as *Amazon Linux*

- Security group open to port 80 for all HTTP is selected.

- command for the user data is attached, see image below.

![ASG](./img/18..png)

- The Lunch template is them attached to the Auto Scaling Group.

![ASG](./img/19.png)

- The next step is to add the load balancer that was previously created, and enable Elastic Load Balancer health check.

- Select desired capacity of scaling group as 2, minimum as 1 and maximum as 3.

- for the Scaling policies, we will be selecting *No scaling policy* and create Auto Scaling Group.

![ASG](./img/20.png)

> ## Testing 3

- we will be opening the Auto Scaling Group and viewing the instance management.
Under this tab, we can see our desired number of instances (2) has been created upfront

![ASG](./img/21.png)

We can also see these 2 instances have been registered with the target group automatically.

![ASG](./img/22.png)

if we go to our load balancer, and copy the DNS name like we previously tested, we can see the same result. we are still able to toggle between the different hosts. 

![ASG](./img/23.png)

If one of the servers go down, the Auto Scaling Group will initialize another instance to replace the saver that is down. this is becasue orur desired capacity was set as 2


![ASG](./img/24.png)


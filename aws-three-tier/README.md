# Problem Statement

**Create a three tier architechture where a public-facing Application Load Balancer forwards client traffic to our web tier EC2 instances. The web tier is running Nginx webservers that are configured to serve a React.js website and redirects our API calls to the application tier’s internal facing load balancer. The internal facing load balancer then forwards that traffic to the application tier, which is written in Node.js. The application tier manipulates data in an Aurora MySQL multi-AZ database and returns it to our web tier. Load balancing, health checks and autoscaling groups are created at each layer to maintain the availability of this architecture.**

![image](<aws 3 tier.drawio.png>)

## VPC And Networking Setup

**Create a VPC, make sure VPC only is selected, and fill out the VPC Settings with a Name tag and a CIDR range of your choice.**
```
10.0.0.0/16
```

![alt text](<Screenshot 2024-03-16 105416.png>)

**Create 6 subnets three in each ap-south-1a and ap-south-1b**

![alt text](<Screenshot 2024-03-16 110114.png>)

![alt text](<Screenshot 2024-03-16 110138.png>) 

**Six subnets are:-**
```
Public-Web-Subnet-AZ-1 - 10.0.0.0/24
Public-Web-Subnet-AZ-2 - 10.0.1.0/24
Private-App-Subnet-AZ-1  - 10.0.2.0/24
Private-App-Subnet-AZ-2 - 10.0.3.0/24
Private-DB-Subnet-AZ-1 - 10.0.4.0/24
Private-DB-Subnet-AZ-2 - 10.0.5.0/24
```
**Then we create a internet gateway and attach it to VPC**

![alt text](<Screenshot 2024-03-16 110259.png>)
![alt text](<Screenshot 2024-03-16 110331.png>)

**Navigate to Route Tablesand  click Create route table, let’s create one route table for the web layer public subnets and name it accordingly.**

![alt text](<Screenshot 2024-03-16 110907.png>)

**Then edit routning config and add internet gateway route** 

![alt text](<Screenshot 2024-03-16 110936.png>) 

**After that add the public subnets to the route table**
![alt text](<Screenshot 2024-03-16 111003.png>)

**Note:** Similarly create route table for private subnet and add private app subnets to it.

**We will now create 5 security groups**

**"internet-facing-lb" security group and add inbound rule HTTP port 80**

![alt text](<Screenshot 2024-03-16 111445.png>) 

**"WebTierSG" security group and add inbound rule HTTP port 80 from "internet-facing-lb" security group**

![alt text](<Screenshot 2024-03-16 111657.png>) 

**"internal-lbsg" security group and add inbound rule HTTP port 80 from "WebTierSG" security group**

![alt text](<Screenshot 2024-03-16 111852.png>) 

**"privateinstacesg" security group and add inbound rule Custom TCP port 4000 from "internal-lbsg" security group**

![alt text](<Screenshot 2024-03-16 112441.png>)

**"DBSG" security group and add inbound rule TCP port 3306 from "privateinstacesg" security group**

![alt text](<Screenshot 2024-03-16 112705.png>) 

## Database Setup 
# Problem Statement

**Create a three tier architecture where a public-facing Application Load Balancer forwards client traffic to our web tier EC2 instances. The web tier is running Nginx web servers that are configured to serve a React.js website and redirects our API calls to the application tier’s internal facing load balancer. The internal facing load balancer then forwards that traffic to the application tier, which is written in Node.js. The application tier manipulates data in an Aurora MySQL multi-AZ database and returns it to our web tier. Load balancing, health checks and auto scaling groups are created at each layer to maintain the availability of this architecture.**

![alt text](<screenshots/aws 3 tier.drawio.png>)

## VPC And Networking Setup

**Create a VPC, make sure VPC only is selected, and fill out the VPC Settings with a Name tag and a CIDR range of your choice.**
```
10.0.0.0/16
```

![alt text](<screenshots/Screenshot 2024-03-16 105416.png>)

**Create 6 subnets three in each ap-south-1a and ap-south-1b**

![alt text](<screenshots/Screenshot 2024-03-16 110114.png>)

![alt text](<screenshots/Screenshot 2024-03-16 110138.png>) 

**Six subnets are:-**
```
Public-Web-Subnet-AZ-1 - 10.0.0.0/24
Public-Web-Subnet-AZ-2 - 10.0.1.0/24
Private-App-Subnet-AZ-1  - 10.0.2.0/24
Private-App-Subnet-AZ-2 - 10.0.3.0/24
Private-DB-Subnet-AZ-1 - 10.0.4.0/24
Private-DB-Subnet-AZ-2 - 10.0.5.0/24
```
**Then we create a internet gateway and attach it to VPC**

![alt text](<screenshots/Screenshot 2024-03-16 110259.png>)
![alt text](<screenshots/Screenshot 2024-03-16 110331.png>)

**Navigate to Route Tables and  click Create route table, let’s create one route table for the web layer public subnets and name it accordingly.**

![alt text](<screenshots/Screenshot 2024-03-16 110907.png>)

**Then edit routing config and add internet gateway route** 

![alt text](<screenshots/Screenshot 2024-03-16 110936.png>) 

**After that add the public subnets to the route table**
![alt text](<screenshots/Screenshot 2024-03-16 111003.png>)

**Note:** Similarly create route table for private subnet and add private app subnets to it.

**We will now create 5 security groups**

**"internet-facing-lb" security group and add inbound rule HTTP port 80**

![alt text](<screenshots/Screenshot 2024-03-16 111445.png>) 

**"WebTierSG" security group and add inbound rule HTTP port 80 from "internet-facing-lb" security group**

![alt text](<screenshots/Screenshot 2024-03-16 111657.png>) 

**"internal-lbsg" security group and add inbound rule HTTP port 80 from "WebTierSG" security group**

![alt text](<screenshots/Screenshot 2024-03-16 111852.png>) 

**"privateinstacesg" security group and add inbound rule Custom TCP port 4000 from "internal-lbsg" security group**

![alt text](<screenshots/Screenshot 2024-03-16 112441.png>)

**"DBSG" security group and add inbound rule TCP port 3306 from "privateinstacesg" security group**

![alt text](<screenshots/Screenshot 2024-03-16 112705.png>) 

**Create a NAT gateway and add the route to private routing table**

![alt text](<screenshots/Screenshot 2024-03-17 092114.png>)

## Database Setup 

**Navigate to the RDS dashboard in the AWS console and click on Subnet groups on the left hand side. Click Create DB subnet group.**

![alt text](<screenshots/Screenshot 2024-03-16 113226.png>) 

**Provide group name and select vpc and db subnets**

![alt text](<screenshots/Screenshot 2024-03-16 113404.png>)

![alt text](<screenshots/Screenshot 2024-03-16 113414.png>)

![alt text](<screenshots/Screenshot 2024-03-16 113449.png>)

**Navigate to Databases on the left hand side of the RDS dashboard and click Create database.**

![alt text](<screenshots/Screenshot 2024-03-16 113523.png>) 

**Select standard create and select MySQL-Compatible Amazon Aurora then select Dev/Test under the templates**

![alt text](<screenshots/Screenshot 2024-03-17 091533.png>)

![alt text](<screenshots/Screenshot 2024-03-17 091547.png>)

**Selct the my-vpc which we created and select the db subnet group**

![alt text](<screenshots/Screenshot 2024-03-17 091608.png>)

**Choose the instance type and select reader node in different az and then click on create**

![alt text](<screenshots/Screenshot 2024-03-17 091558.png>)

![alt text](<screenshots/Screenshot 2024-03-17 091659.png>)

# App Tier Setup

**Go to Launch instances and launch an EC2**

![alt text](<screenshots/Screenshot 2024-03-17 091909.png>)

**Select my-vpc and private subnet 1 also select privateinstancesg security group and then click on create**

![alt text](<screenshots/Screenshot 2024-03-17 091943.png>)

**Create a Bastion host in the public subnet with all traffic enabled. This is required to connect to other instances**

![alt text](<screenshots/Screenshot 2024-03-17 092303.png>)

**Connect to the Bastion host and from there go to the app tier instance.**

![alt text](<screenshots/Screenshot 2024-03-17 092414.png>) 
![alt text](<screenshots/Screenshot 2024-03-17 092511.png>) 
![alt text](<screenshots/Screenshot 2024-03-17 092645.png>) 
![alt text](<screenshots/Screenshot 2024-03-17 092711.png>) 

**Install the mariadb105-server on the app instance.**
```
sudo yum install mariadb105-server -y
```
![alt text](<screenshots/Screenshot 2024-03-17 093013.png>) 

**Then connect to database using endpoint.**
```
mysql -h CHANGE-TO-YOUR-RDS-ENDPOINT -u CHANGE-TO-USER-NAME -p
```
![alt text](<screenshots/Screenshot 2024-03-17 093041.png>) 

**Create a database called webappdb**
```
CREATE DATABASE webappdb;   
```
**You can verify that it was created correctly**
```
SHOW DATABASES;
```
**Create a data table:**
```
USE webappdb;    
```
![alt text](<screenshots/Screenshot 2024-03-17 093117.png>) 

**Then, create the following transactions table by executing this create table command:**
```
CREATE TABLE IF NOT EXISTS transactions(id INT NOT NULL
AUTO_INCREMENT, amount DECIMAL(10,2), description
VARCHAR(100), PRIMARY KEY(id));    
```
**Verify the table was created:**
```
SHOW TABLES;
```

**Insert data into table for testing later:**
```
INSERT INTO transactions (amount,description) VALUES ('400','groceries');   
```

**Verify that your data**
```
SELECT * FROM transactions;
```

![alt text](<screenshots/Screenshot 2024-03-17 093153.png>) 

**We will create a S3 bucket which will contain configuration**

![alt text](<screenshots/Screenshot 2024-03-17 093504.png>)

**Create a role and attach S3ReadOnlyRole and Ec2ManagedInstanceCore permission to it.**

![alt text](<screenshots/Screenshot 2024-03-17 093800.png>) 

**The first thing we will do is update our database credentials for the app tier. To do this, open the application-code/app-tier/DbConfig.js file from the github repo. Fill this in with the credentials, the writer endpoint of the database as the hostname, and webappdb for the database. Save the file.**

![alt text](screenshots/s3.png)

**Now move to the app instance and run th following commands for setup**

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
```
Next, install Node.js and make sure it's being used
```
nvm install 16
nvm use 16
```
PM2 is a daemon process manager that will keep our node.js app running when we exit the instance or if it is rebooted. Install that as well.
```
npm install -g pm2   
```
Now we need to download our code from our s3 buckets onto our instance. In the command below, replace BUCKET_NAME with the name of the bucket you uploaded the app-tier folder to:
```
cd ~/
aws s3 cp s3://BUCKET_NAME/app-tier/ app-tier --recursive
```
Navigate to the app directory, install dependencies, and start the app with pm2.
```
cd ~/app-tier
npm install
pm2 start index.js
```
To make sure the app is running correctly run the following:
```
pm2 list
```
If you see a status of online, the app is running. If you see errored, then you need to do some troubleshooting. To look at the latest errors, use this command:
```
pm2 logs
```

Right now, pm2 is just making sure our app stays running when we leave the SSM session. However, if the server is interrupted for some reason, we still want the app to start and keep running. This is also important for the AMI we will create:
```
pm2 startup
```
After running this you will see a message similar to this.

[PM2] To setup the Startup Script, copy/paste the following command: 
```
sudo env PATH=$PATH:/home/ec2-user/.nvm/versions/node/v16.0.0/bin /home/ec2-user/.nvm/versions/node/v16.0.0/lib/node_modules/pm2/bin/pm2 startup systemd -u ec2-user —hp /home/ec2-user
```
After that, save the current list of node processes with the following command:
```
pm2 save
```
**Now let's run a couple tests to see if our app is configured correctly.**

**To hit out health check endpoint**
```
curl http://localhost:4000/health
```
**The response should looks like the following:**
```
"This is the health check"
```
**Next, test your database connection**
```
curl http://localhost:4000/transaction
```
You should see a response containing the test data we added earlier:
```
{"result":[{"id":1,"amount":400,"description":"groceries"},{"id":2,"amount":100,"description":"class"},{"id":3,"amount":200,"description":"other groceries"},{"id":4,"amount":10,"description":"brownies"}]}
```
**If you see both of these responses, then your networking, security, database and app configurations are correct.**

# Internal Load Balancing and Autoscaling


# Capstone Project - 1: Deploying a Multi-Tier Website Using AWS EC2

## Step:1 Create a EC2 Instance. 
![alt text](image.png)

## Step 2: Select the instance type and key-pair. 
![alt text](image-1.png)

## Step:3: Create a security group allow ssh, http and https. 
![alt text](image-2.png)

## Step:4 Login to the EC2 instance and update the machine. 
![alt text](image-3.png)

## Step:5 Install apache2 web server. 
![alt text](image-4.png)

## Step:6 Add the php repository. 
![alt text](image-5.png)

## Step:7 Install php and mysql-client. 
![alt text](image-6.png)

## Step:8 Navigate to RDS and create a database. 
![alt text](image-30.png)

## Step:9 Select standard create and MySql engine. 
![alt text](image-29.png)

## Step:10 Select Free tier and provide a name of db instance. 
![alt text](image-28.png)

## Step:11 Select public access as ‘yes’ in this case. 
![alt text](image-27.png)

## Step:12 Provide initial database name and leave rest as default.
![alt text](image-26.png) 

## Step:13 In database security group add a rule that accepts requests from security group of EC2 instances on port 3306. 
![alt text](image-25.png)

## Step:14 After creation of database login to ec2 instance and try to access the database. 
![alt text](image-24.png)

## Step:15 Check if data is present in the database. 
![alt text](image-23.png)

## Step: 16 Now navigate to /var/www/html and create two files as shown below.
![alt text](image-22.png) 

## Step:17 Type this content in the index.html as shown below. 
![alt text](image-21.png)

## Step:18 Type this content in the insert_students.php as shown below. 
![alt text](image-20.png)

## Step:19 After that start the apache2 server. You will find this form. 
![alt text](image-19.png)

## Step:20 Fill some data in the form. 
![alt text](image-18.png)

## Step:21 You can see record has been successfully created. 
![alt text](image-17.png)

## Step:22 Now check the data in the intel table which we created. You can clearly see the data has been populated in the table. 
![alt text](image-16.png)

## Step:23 Next we will create image the of the server. 
![alt text](image-15.png)

## Step:24 provide name and all other details. 
![alt text](image-14.png)

## Step:25 Next, we will create a launch template for autoscaling, enter all the details required. 
![alt text](image-13.png)

## Step:26 Now we will create Autoscaling group and we will also create load balancer in the same step. For ASG minimum instances will be 2. 
![alt text](image-12.png)
![alt text](image-11.png) 
![alt text](image-10.png) 
![alt text](image-9.png) 
![alt text](image-8.png)

## Step:27 Final output. 
![alt text](image-7.png)









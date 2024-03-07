# Project Description
**We are planning to set up three servers: one for Jenkins, one for Ansible, and one for Docker. We will use Jenkins to create a pipeline that will fetch code from a Git repository, build the code, and then use Ansible to build the image. Finally, it will run the container on the Docker server.**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/0dd7558a-15eb-46e8-9493-a85b74f0bdf7)

## Create 3 EC2 instances for Jenkins, Ansible and Docker.

### Jenkins server configuration

**Run the following command:-** 
```
sudo su -
```
```
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo; rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```
```
yum upgrade
```
```
yum install java-17-amazon-corretto jenkins -y
```
```
systemctl enable --now jenkins
```

**Connect to http://[public_ip]:8080 from your browser.** 

![image](https://github.com/rishavdas494/aws_project/assets/72242235/c203a3c1-2158-4ece-80cd-72bd1648c209)

**As prompted, enter the password found in /var/lib/jenkins/secrets/initialAdminPassword.**

**Use the following command to display this password:**
```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

![image](https://github.com/rishavdas494/aws_project/assets/72242235/5cd0a44d-a1bf-4d58-9840-f02976517cb5)

**Install the Following plugins**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/70e95810-805b-4ee4-a280-7593309c6d8c)

**Then restart jenkins
Install git, maven on jenkins server
Use this command to install git.**
```
yum install git -y
```
**Then open bashrc file to set M2_HOME**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/d39b80d2-1522-49b2-bfde-874194909b74)

**Open Jenkins console and go to tools and set git and maven locations.**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/e7831d7b-915e-4424-8086-fa9179308b0f)

![image](https://github.com/rishavdas494/aws_project/assets/72242235/8c7fa8a7-bbc1-471d-ac58-33925809825c)

**Go to systems settings and set details for ansible server. This is required for to publish artifact over ssh.**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/9f9503cf-5995-48dd-9367-df7cefa25da8)

### Ansible server configuration

**Install ansible-core, docker package**
```
yum install ansible-core docker -y
```
**Start Docker and enable it at boot time**
```
systemctl enable --now docker
```
**Create a ansadmin user**
```
useradd ansadmin
```
**Add user to docker group**
```
usermod -aG docker ansadmin
```
**Open sshd_config file search for PasswordAuthentication and set it yes.**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/fc7dd0db-7d71-420d-81bd-6be95b6f4919)

**Create a directory in /opt/docker**
```
mkdir /opt/docker
```
**Edit user and group of the directory to ansadmin.**
```
chown -R ansadmin. /opt/docker
```
**Run docker login command to login to docker-hub**

**Create a file in /etc/ansible/hosts and add the following**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/591fa64d-8c68-4217-b629-64cf2781fb84)

### Docker server configuration

**Install docker package**
```
yum install docker -y
```
**Start Docker and enable it at boot time**
```
systemctl enable --now docker
```
**Create a ansadmin user**
```
useradd ansadmin
```
**Add user to docker group**
```
usermod -aG docker ansadmin
```
**Open sshd_config file search for PasswordAuthentication and set it yes.
Create a directory in /opt/docker**
```
mkdir /opt/docker
```
**Edit user and group of the directory to ansadmin.**
```
chown -R ansadmin. /opt/docker
```
**Then run ssh-keygen on ansible server using ansadmin user.
It will generate ssh key pairs.**

**Then run ssh-copy-id ansadmin@private_ip_of_docker_server
Again run ssh-copy-id localhost**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/a883a20b-fcc2-4cd4-ad0e-b6b31fc81436)

![image](https://github.com/rishavdas494/aws_project/assets/72242235/2c184b58-4027-421e-84e9-520028f7d2e4)

**Write two playbook in the ansible server-**
**1.For creating and pushing the image to docker hub.**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/09d9a8ac-ba87-4faa-b4ea-54adf7ca05a4)

**2.For pulling and running container in the server.**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/7192ca74-a20b-4fda-9cfe-01b85f5033a4)

**Now open Jenkins and configure the pipeline as shown below:-.**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/df60b56f-144e-4fa2-ae52-7ea01fea2fbf)

![image](https://github.com/rishavdas494/aws_project/assets/72242235/d6f81a6c-5909-4fd2-b973-b9e344a1fcd4)

![image](https://github.com/rishavdas494/aws_project/assets/72242235/ab7663f5-b700-4742-abfc-ef2884c921e9)

![image](https://github.com/rishavdas494/aws_project/assets/72242235/27d04b79-3822-4299-af68-aba00cdb83ee)

**🥇Final output🥇**

![image](https://github.com/rishavdas494/aws_project/assets/72242235/89307382-6139-45c7-b48f-a256ab1c238b)


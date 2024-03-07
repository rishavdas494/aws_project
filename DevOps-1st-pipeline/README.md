Create 3 EC2 instances for Jenkins, Ansible and Docker.

Jenkins server configuration

Run the following command
# sudo su -
# yum update –y
# wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
# rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
# yum upgrade
# dnf install java-17-amazon-corretto -y
# yum install jenkins -y
# systemctl enable jenkins

Connect to http://[public_ip]:8080 from your browser. 


As prompted, enter the password found in /var/lib/jenkins/secrets/initialAdminPassword.

Use the following command to display this password:
# cat /var/lib/jenkins/secrets/initialAdminPassword




Then restart jenkins
Install git, maven on jenkins server
Use this command to install git.
# yum install git -y








Then open bashrc file to set M2_HOME



Open Jenkins console and go to tools and set git and maven locations.





Go to systems settings and set details for ansible server. This is required for to publish artifact over ssh. 



Ansible server configuration

Install ansible-core, docker package
# yum install ansible-core docker -y
Start Docker and enable it at boot time
# systemctl enable --now docker
Create a ansadmin user
# useradd ansadmin
Add user to docker group
# usermod -aG docker ansadmin
Open sshd_config file search for PasswordAuthentication and set it yes.

Create a directory in /opt/docker
# mkdir /opt/docker
Edit user and group of the directory to ansadmin.
#chown -R ansadmin. /opt/docker
Run docker login command to login to docker-hub

Create a file in /etc/ansible/hosts and add the following 




Docker server configuration

Install docker package
# yum install docker -y
Start Docker and enable it at boot time
# systemctl enable --now docker
Create a ansadmin user
# useradd ansadmin
Add user to docker group
# usermod -aG docker ansadmin
Open sshd_config file search for PasswordAuthentication and set it yes.
Create a directory in /opt/docker
# mkdir /opt/docker
Edit user and group of the directory to ansadmin.
#chown -R ansadmin. /opt/docker


Then run ssh-keygen on ansible server using ansadmin user.
It will generate ssh key pairs.
Then run ssh-copy-id ansadmin@private_ip_of_docker_server
Again run ssh-copy-id localhost






Write two playbook in the ansible server-
1.For creating and pushing the image to docker hub.






2.For pulling and running container in the server.



Now open Jenkins and configure the pipeline as shown below.








Final output

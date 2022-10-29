# Steps to setup Jenkins server on AWS

- Create a security group in AWS
    - Give it a name e.g "jenkins-master"
    - add ssh, http, https rule
    - add a name tag, e.g. "jenkins-master"

- Create a key pair in AWS
    - Select .pem format
    - Give it a name e.g "jenkins-master"

- Create EC2 instance
    - Select ubuntu 20.04 LTS AMI
    - Select instance type e.g "t2.micro"
    - Make sure Auto Assign public IP is enabled
    - Select the security group created on the above step
    - Select the key pair created on the above step
    - Create a tag e.g "jenkins-master"
    - Launch the instance

- Install Java, Jenkins and Nginx
    - ssh to the created instance
    - execute below commands
    ```
    sudo su
    wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
    echo "deb https://pkg.jenkins.io/debian-stable binary/" > /etc/apt/sources.list.d/jenkins.list
    apt update && apt upgrade -y
    apt -y install openjdk-11-jdk nginx
    apt -y install jenkins

    systemctl status nginx  | grep Active
    systemctl status jenkins  | grep Active
    ```

- Configuring Nginx
    - Go to the public DNS of the instance using "http" : Nginx Welcome page
    - Execute below commands on the server
    ```
    unlink /etc/nginx/sites-enabled/default
    # create file jenkins.conf 
    vim /etc/nginx/conf.d/jenkins.conf
    nginx -t
    systemctl reload nginx
    ```
    - Go to the public DNS of the instance using "http" : Unlock Jenkins page

- Configuring jenkins
    - In the server go to ```/var/lib/jenkins/secrets/initialAdminPassword``` to get the first password
    - Put the first password on the Unlock jenkins page and follow the instructions on the page
    - Create the admin account 


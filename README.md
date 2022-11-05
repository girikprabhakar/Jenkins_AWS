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
    - execute below commands from the [install.txt](https://github.com/girikprabhakar/Jenkins_AWS/blob/AWS/install.txt)
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


# Steps to setup Jenkins build-server on AWS
- Create IAM role for the build server
    - Create EC2 role for the build server.
    - Attach relevant policies to the role
    - Add relevant Name and tags

- Create Security Group  and Key Pair for the build server
    - Create Security Group 
    - Add Name eg: build-server
    - Add rule for type ssh and put Jenkins master's security group as source
    - Create security group
    - Create .pem  key-pair and name it `build-server`

- Steps to spin up build-server on AWS
    - Launch ec2 instance with AMazon Linux 2 AMI
    - Add build-server IAM role
    - Attach build-server security group
    - In the User data, add the contents of the file named [user_data.txt](https://github.com/girikprabhakar/Jenkins_AWS/blob/AWS/user_data.txt) to install dependencies
    - Add relevant storage and tags
    - Launch instance

- Steps to add build-server to Jenkins master on AWS
    - From the Jenkins master dashboard -> Manage Jenkins -> Manage Credentials -> Global (under stores scoped to Jenkins) -> Add Credentials
    - Kind -> ssh username with a private key
    - ID: build-server
    - Username: ec2-user
    - Private-key -> enter directly-> add -> paste the content of build-server's private key
    - ok

- Connect Jenkins master with the build server
    - Copy the private DNS of the build server
    - On the Jenkins Dashboard , select `Build Executor Status`
    - Select New Node
    - Provide Node name, e.g: build-server-1 and select permanent agent
    - Add relevant description
    - Add number of executors (match with the number of CPUs available on the server)
    - Add remote directory `/home/ec2-user` (depends on the OS)
    - Add labels to detect the build server
    - Usage: Use this node as much as possible
    - Launch Method: Launch agent via ssh 
    - Host: add private DNS of the Build Server
    - Credentials: ec2-user
    - Host Key Verification Strategy: Non verifying Verification Strategy
    - Availability: keep this agent online as much as possible
    - Save
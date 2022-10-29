Change to the root user after logging in:

    sudo su -

Add the aptitude key for the Jenkins application:

    wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

Add the Jenkins debian repo to the aptitude sources list:

    echo "deb https://pkg.jenkins.io/debian-stable binary/" > /etc/apt/sources.list.d/jenkins.list

Update the source lists and upgrade any out of date packages:

    apt update
    apt -y upgrade

Install the software for the Jenkins master:  openjdk-11-jdk, nginx, and jenkins.

Install JDK and nginx first:
    apt -y install openjdk-11-jdk nginx

Then install jenkins:

    apt -y install jenkins

Confirm that jenkins and nginx are installed:

    systemctl status nginx  | grep Active
    systemctl status jenkins  | grep Active


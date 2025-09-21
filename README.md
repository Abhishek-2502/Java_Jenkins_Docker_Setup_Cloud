# Java, Jenkins, Docker, Docker Compose and Docker Hub Setup on an Instance with Ubuntu

This guide outlines the steps to install and configure Java, Jenkins, Docker, Docker Compose and Docker Hub on Ubuntu. 

## Prerequisites

- An instance with Ubuntu.
- A user with `sudo` privileges.

## Steps

### 1. Update the System

Start by updating your system to ensure all packages are up-to-date.

```bash
sudo apt update
```

### 2. Install Java

Jenkins requires Java, so install OpenJDK 17.

```bash
sudo apt install openjdk-17-jdk -y
```

Verify the Java installation:

```bash
java -version
```

Ensure it shows something like this:

```
openjdk version "17.0.x" 2021-09-14
OpenJDK Runtime Environment (build 17.0.x+xx)
OpenJDK 64-Bit Server VM (build 17.0.x+xx, mixed mode, sharing)
```

### 3. Install Jenkins

#### 3.1 Add Jenkins GPG Key

You need to add Jenkins' official GPG key to your system for package verification.

```bash
wget -O - https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

#### 3.2 Add Jenkins Repository

Add the Jenkins repository to your sources list.

```bash
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

#### 3.3 Install Jenkins

Update your package lists and install Jenkins:

```bash
sudo apt update
sudo apt-get install jenkins -y
```

#### 3.4 Start Jenkins

Enable Jenkins to start automatically on boot, and then start the service:

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

Check the Jenkins service status:

```bash
sudo systemctl status jenkins
```

If the service is running, it should show the status as `active (running)`.

#### 3.5 Configure AWS Security Group, GCP Firewall, etc

Ensure that you can access Jenkins by modifying the inbound rules of your instance's security group.

- Add a custom TCP rule for port `8080`.
- Set the source to `Anywhere-IPv4` to allow access from anywhere.



#### 3.6 Access Jenkins Web Interface

Open your browser and navigate to the following URL, replacing `<PublicIPv4>` with your instance's public IPv4 address:

```
http://<PublicIPv4>:8080
```

#### 3.7 Retrieve Jenkins Admin Password

The first time you access Jenkins, it will ask for an initial admin password. Retrieve the password by running this command on your instance:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password and paste it into the login form on the Jenkins web page.

#### 3.8 Set Up Admin Username and Password

After the plugin installation is complete, you’ll be asked to create an admin user.

- **Username**: Abhishek-2502
- Choose your preferred password.

#### 3.9 Install Suggested Plugins

Once logged in, Jenkins will prompt you to install the suggested plugins. Click on "Install suggested plugins" to continue. Once the setup is complete, Jenkins will be ready to use

### 4. Install Docker

Installing Docker

```bash
sudo apt install docker.io -y
```

Enable Docker at Startup

```bash
sudo system enable --now docker
```

Checking Docker Version

```bash
docker version
```

Giving Permission

```bash
sudo usermod -aG docker $USER && newgrp docker
```

Giving Permission

```bash
sudo usermod -aG docker jenkins
```

Restarting Jenkins

```bash
sudo systemctl restart jenkins
```

Rebooting Instance

```bash
sudo reboot
```

### 5. Connect to Docker Hub

First you need to create an account on `https://hub.docker.com/`.

Login to Docker Hub
```bash
docker login
```

### 6. Install Docker Compose

```bash
sudo apt-get install docker-compose -y
```

### 7. Install Jenkins via Docker

#### 7.1 Get Docker image of Jenkins:
```bash
docker run -d \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name jenkins-docker \
  jenkins/jenkins:lts
```

#### 7.2 Retrieve Jenkins Admin Password
```bash
docker exec jenkins-docker cat /var/jenkins_home/secrets/initialAdminPassword
```
---

### Other:

Uninstall Docker:
```bash
sudo apt remove -y docker docker-engine docker.io containerd runc
sudo apt-get purge -y docker.io
sudo apt autoremove -y
hash -r
```

Remove docker data (containers, images, volumes, configs)
```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

Uninstall Jenkins:
```bash
sudo apt remove --purge -y jenkins
sudo apt autoremove -y
hash -r
```

Remove Jenkins data
```bash
sudo rm -rf /var/lib/jenkins
sudo rm -rf /var/cache/jenkins
sudo rm -rf /var/log/jenkins
```
---

## Note:

- Always ensure your instance is properly secured with the necessary firewall rules and access controls.
- The instance should be monitored for security and performance, especially when hosting Jenkins for production workloads.

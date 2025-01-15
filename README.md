# Jenkins Debian Packages and Integration with GitHub

This guide provides steps to set up Jenkins using Debian packages, integrate it with GitHub using SSH keys, and deploy a Node.js application using Docker.

## Installing Jenkins on Debian

### Add Jenkins Repository Key
1. Download the Jenkins key and add it to your system:
   ```bash
   sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
       https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
   ```

2. Add the Jenkins apt repository:
   ```bash
   echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
       https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
       /etc/apt/sources.list.d/jenkins.list > /dev/null
   ```

### Install Jenkins
1. Update your local package index:
   ```bash
   sudo apt-get update
   ```
2. Install required dependencies and Jenkins:
   ```bash
   sudo apt-get install fontconfig openjdk-17-jre
   sudo apt-get install jenkins
   ```

### Verify Java Installation
1. Check the installed Java version:
   ```bash
   java -version
   ```
   Example output:
   ```
   openjdk version "17.0.13" 2024-10-15
   OpenJDK Runtime Environment (build 17.0.13+11-Debian-2)
   OpenJDK 64-Bit Server VM (build 17.0.13+11-Debian-2, mixed mode, sharing)
   ```

## GitHub Integration with Jenkins

### Generate SSH Keys
1. Generate SSH keys on your system:
   ```bash
   ssh-keygen
   ```
2. Navigate to the `.ssh` directory and list the keys:
   ```bash
   cd ~/.ssh
   ls
   ```
   You should see files such as `id_rsa` (private key) and `id_rsa.pub` (public key).

3. Display the keys:
   ```bash
   cat id_rsa      # Private key
   cat id_rsa.pub  # Public key
   ```

### Configure GitHub
1. In your GitHub account, go to **Settings > SSH and GPG keys**.
2. Add a new SSH key:
   - Name: `jenkin_project`
   - Paste the public key (`id_rsa.pub`) and save.
3. Authenticate with a verification code or password.

### Configure Jenkins
1. In Jenkins, create a new **Freestyle Project**.
2. In the **Source Code Management** section, select **Git** and provide the Git repository URL.
3. Configure credentials:
   - Add Jenkins credentials.
   - ID: `github_jenkin`.
   - Username: (Use the output of `whoami` on your Linux system, e.g., `ubuntu`).
   - Private Key: Paste the private key (`id_rsa`).
4. Specify the branch (e.g., `master`) and save.

## Build and Deploy Node.js Application

### Clone the Repository
1. In Jenkins, after triggering a build, check the workspace path in the console output (e.g., `var/lib/jenkins/workspace/todo`).
2. Navigate to the workspace on your Linux system:
   ```bash
   cd /var/lib/jenkins/workspace/todo
   ls
   ```
   You should see the files cloned from the GitHub repository.

### Install Dependencies
1. Run `npm install` to install Node.js dependencies:
   ```bash
   sudo npm install
   ```

### Create a Dockerfile
1. If a Dockerfile exists, remove it:
   ```bash
   rm Dockerfile
   ```
2. Create a new Dockerfile with the following content:
   ```dockerfile
   FROM node:12-alpine
   WORKDIR /app
   COPY . .
   RUN npm install
   EXPOSE 8000
   CMD ["node", "app.js"]
   ```

### Add Build Steps in Jenkins
1. In Jenkins, add the following build steps:
   ```bash
   docker build -t nodeapp .
   docker run -d --name node_container -p 8000:8000 nodeapp
   ```

### Access the Application
1. Access the Node.js application running on port 8000 in your browser:
   ```
   http://<your_server_ip>:8000
   ```



5. Adding a GitHub Webhook for Automated Builds

Configure the Webhook

Go to your GitHub repository settings.

Navigate to Webhooks > Add Webhook.

Enter the Jenkins webhook URL:

http://<jenkins-server-ip>:8080/github-webhook/

Select the application/json content type.

Choose events to trigger the webhook (e.g., Push events).

Save the webhook.

Test the Webhook

Push a change to the repository and verify that Jenkins triggers the build automatically.


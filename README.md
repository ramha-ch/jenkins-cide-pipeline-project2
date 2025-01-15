# node-todo-cicd

Run these commands:


`sudo apt install nodejs`


`sudo apt install npm`


`npm install`

`node app.js`

or Run by docker compose

test

Jenkins Debian Packages and Integration with GitHub

This guide provides steps to set up Jenkins using Debian packages, integrate it with GitHub using SSH keys, and deploy a Node.js application using Docker.

Installing Jenkins on Debian

Add Jenkins Repository Key

Download the Jenkins key and add it to your system:

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

Add the Jenkins apt repository:

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

Install Jenkins

Update your local package index:

sudo apt-get update

Install required dependencies and Jenkins:

sudo apt-get install fontconfig openjdk-17-jre
sudo apt-get install jenkins

Verify Java Installation

Check the installed Java version:

java -version

Example output:

openjdk version "17.0.13" 2024-10-15
OpenJDK Runtime Environment (build 17.0.13+11-Debian-2)
OpenJDK 64-Bit Server VM (build 17.0.13+11-Debian-2, mixed mode, sharing)

GitHub Integration with Jenkins

Generate SSH Keys

Generate SSH keys on your system:

ssh-keygen

Navigate to the .ssh directory and list the keys:

cd ~/.ssh
ls

You should see files such as id_rsa (private key) and id_rsa.pub (public key).

Display the keys:

cat id_rsa      # Private key
cat id_rsa.pub  # Public key

Configure GitHub

In your GitHub account, go to Settings > SSH and GPG keys.

Add a new SSH key:

Name: jenkin_project

Paste the public key (id_rsa.pub) and save.

Authenticate with a verification code or password.

Configure Jenkins

In Jenkins, create a new Freestyle Project.

In the Source Code Management section, select Git and provide the Git repository URL.

Configure credentials:

Add Jenkins credentials.

ID: github_jenkin.

Username: (Use the output of whoami on your Linux system, e.g., ubuntu).

Private Key: Paste the private key (id_rsa).

Specify the branch (e.g., master) and save.

Build and Deploy Node.js Application

Clone the Repository

In Jenkins, after triggering a build, check the workspace path in the console output (e.g., var/lib/jenkins/workspace/todo).

Navigate to the workspace on your Linux system:

cd /var/lib/jenkins/workspace/todo
ls

You should see the files cloned from the GitHub repository.

Install Dependencies

Run npm install to install Node.js dependencies:

sudo npm install

Create a Dockerfile

If a Dockerfile exists, remove it:

rm Dockerfile

Create a new Dockerfile with the following content:

FROM node:12-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 8000
CMD ["node", "app.js"]

Add Build Steps in Jenkins

In Jenkins, add the following build steps:

docker build -t nodeapp .
docker run -d --name node_container -p 8000:8000 nodeapp

Access the Application

Access the Node.js application running on port 8000 in your browser:

http://<your_server_ip>:8000

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


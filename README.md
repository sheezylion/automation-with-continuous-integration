# Tooling Website deployment automation with Continuous Integration using Jenkins
Introduction to Jenkins

Jenkins, originally developed by Kohsuke Kawaguchi, has evolved into the de facto standard for CI/CD automation, 
empowering development teams worldwide to build, test, and deploy software with unparalleled efficiency and reliability. 
As an automation server, Jenkins orchestrates the entire software delivery pipeline, from source code management to production deployment, 
enabling teams to iterate rapidly, respond to feedback promptly, and deliver value to customers consistently.
Jenkins Setup and Configuration.

In this project we are going to start automating part of our routine tasks with a free and open source automation server - Jenkins. It is one of the mostl popular CI/CD tools.

According to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of 
development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments which is then automatically built and tested before it is 
merged with the shared repository.

## Task
Enhance the architecture prepared in the previous project by adding a **Jenkins** server, configure a job to automatically deploy source codes changes from **Git** to **NFS** server.

Here is how the updated architecture looks.

<img width="743" alt="architecture" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/3561df0c-f2f6-4b39-a500-5d446505d9a6">

#### Step 1 - Install Jenkins server
**1. Create an aws EC2 instance based on Ubuntu Server 24.04 LTS and name it Jenkins**

Results:

<img width="1237" alt="Screenshot 2024-06-25 at 21 27 08" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/bfe0c1b1-4fcd-43b8-a079-c85349256cf1">

<img width="1680" alt="Screenshot 2024-06-25 at 21 27 50" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/24a748c8-8f23-446f-9aef-054e2888e617">

**2. Install JDK since Jenkins is a Java-based application**
- Access the instance

```
ssh -i ~/Downloads/demo-pair.pem ubuntu@35.153.206.247
```

Result:

<img width="842" alt="Screenshot 2024-06-25 at 21 30 32" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/27372643-5c2e-4f9b-8438-91ec0ba20a4c">

- Update the Instance

```
sudo apt-get update
```

Result: 

<img width="1080" alt="Screenshot 2024-06-25 at 21 31 29" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/96b02fdc-d082-49eb-a680-8713037878a3">

- Download the Jenkins key

```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian/jenkins.io-2023.key
```

Result:

<img width="872" alt="Screenshot 2024-06-25 at 21 34 42" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/8825736a-9a21-4fa9-8486-5b386620d9f2">

- Add the Jenkins Repository

```
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

Result:

<img width="843" alt="Screenshot 2024-06-25 at 21 35 48" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/6b7f96c7-838f-4694-88dd-f3b04656cee0">

- Install Java

Jenkins requires Java to run, yet not all Linux distributions include Java by default. 
Additionally, not all Java versions are compatible with Jenkins. Install **OpenJDK 17**

```
sudo apt install fontconfig openjdk-17-jre
```

Result:

<img width="1202" alt="Screenshot 2024-06-25 at 21 37 28" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/e4b8b46d-b718-4fc7-869a-07a90ad2a2e7">

**3. Install Jenkins**

- Update ubuntu

```
sudo apt-get update
```

Result:

<img width="809" alt="Screenshot 2024-06-25 at 21 39 11" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/4c9d419c-ecb4-444d-a7ab-c6f0b512a032">

- Install jenkins

```
sudo apt-get install jenkins -y
```

Result:

<img width="1025" alt="Screenshot 2024-06-25 at 21 40 46" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/16ad9ddf-a7a0-4ac9-8bd8-cd20a5cee681">

- Ensure jenkins is up and running

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

Result:

<img width="1475" alt="Screenshot 2024-06-25 at 21 43 11" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/052fcc4d-ef73-4390-b12b-cc09c1ef8e47">

**4. By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound rule in the EC2 Security Group**

Result:

<img width="1637" alt="Screenshot 2024-06-25 at 21 42 15" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/8f463b4f-dd79-493e-bff3-5a6ba6c830ac">

**5. Perform initial Jenkins setup**

From a browser access http://<Jenkins-Server-Public-IP-Address>:8080 
You will be prompted to provide a default admin password. Retrieve it from the server.

```
http://35.153.206.247:8080
```

Result:

<img width="1292" alt="Screenshot 2024-06-25 at 21 47 30" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/77e92bcb-faa0-4637-8b32-2ad479fd168b">

- To login copy the var directory below, **cat** to copy and paste the password

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Result:

<img width="1441" alt="Screenshot 2024-06-25 at 21 50 00" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/5f329c1c-2a09-4105-bd2a-cb2676fb770b">

Then you will be asked which plugins to install - choose suggested plugins

Result:

<img width="1269" alt="Screenshot 2024-06-25 at 21 51 22" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/c0934906-c239-4957-be42-4b1acea97556">


Once plugins installation is done, create an admin user and you will get the jenkins server address.

Results:

<img width="880" alt="Screenshot 2024-06-25 at 21 52 46" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/3d9d972f-e8e6-475b-a047-2a2f3563c128">

<img width="890" alt="Screenshot 2024-06-25 at 21 52 59" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/3cb8ebd1-8e98-4916-9def-ae6a858cf0fd">

The installation is complete.

<img width="944" alt="Screenshot 2024-06-25 at 21 53 07" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/d9b5987e-e976-4959-ae68-4dd316f33229">

#### Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, we will learn how to configure a simple Jenkins job/project. This job will will be triggered by GitHub webhooks and will execute a build task to retrieve codes from GitHub and store it locally on Jenkins server.

**1. Enable webhooks in your GitHub repository settings.**

On your GitHub repository,

Select Settings > Webhooks > Add webhook

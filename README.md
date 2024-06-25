<img width="1648" alt="Screenshot 2024-06-25 at 23 21 03" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/24dd6fca-3fc2-4a1f-bd60-77a809a31861"># Tooling Website deployment automation with Continuous Integration using Jenkins
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

### Step 1 - Install Jenkins server
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

### Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, we will learn how to configure a simple Jenkins job/project. This job will will be triggered by GitHub webhooks and will execute a build task to retrieve codes from GitHub and store it locally on Jenkins server.

**1. Enable webhooks in your GitHub repository settings.**

On your GitHub repository,

Select Settings > Webhooks > Add webhook

Results:

<img width="1674" alt="Screenshot 2024-06-25 at 22 09 06" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/18e3c72c-2f63-4f05-b14f-e01454c70a02">

<img width="1319" alt="Screenshot 2024-06-25 at 22 12 21" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/89755560-6c0b-4509-b7cd-c539dbb5bd83">

**2. Go to Jenkins web console, click New Item and create a Freestyle project**

Result:

<img width="1156" alt="Screenshot 2024-06-25 at 22 14 38" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/ecaba1a5-3a8e-4170-a371-085e69bedda8">

To connect our GitHub repository, we will need to provide its URL, we can copy from the repository itself

```
https://github.com/sheezylion/tooling
```

In configuration of our Jenkins freestyle project choose Git repository, provide there the link to our Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

Result:

<img width="1554" alt="Screenshot 2024-06-25 at 22 24 03" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/f93f9936-8ce8-4bf5-8923-9c1dbdf9ee66">

Save the configuration and try to run the build. For now we can only do it manually. Click Build Now button. After all was configured correctly, the build was successfull and was seen under #3 You can open the build and check in Console Output if it has run successfully.

Result:

<img width="1330" alt="Screenshot 2024-06-25 at 22 28 18" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/5917cade-d6f8-4496-9dcd-b0fd5087eac6">


But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

**3. Click Configure our job/project and add these two configurations**

Result:

<img width="1545" alt="Screenshot 2024-06-25 at 22 31 25" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/4c04d314-3af5-48c1-850e-c12e13c65aff">

<img width="1518" alt="Screenshot 2024-06-25 at 22 32 39" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/7d0b576a-c47f-492b-8f80-9334eda0636e">

Now, go ahead and make some change in any file in our GitHub repository (e.g. README.MD file) and push the changes to the main branch.

we will see that a new build has been launched automatically by webhook and its results - artifacts, saved on Jenkins server.

Results:

<img width="1578" alt="Screenshot 2024-06-25 at 22 36 35" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/96678577-3c35-4c41-834c-78b77c9edae4">

<img width="1082" alt="Screenshot 2024-06-25 at 22 37 21" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/2310c543-8d83-490a-8a65-869918107520">

We configured an automated Jenkins job that receives files from GitHub by webhook trigger this method is considered as push because the changes are being pushed and files transfer is initiated by GitHub. There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```

### Step 3 - Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are more than 1400 plugins available. now we will need a plugin that is called Publish Over SSH

**1. Install Publish Over SSH plugin.**

On main dashboard, Select Manage Jenkins > Manage Plugins > Available > Search for Publish over SSH and Install without restart.

Result:

<img width="1661" alt="Screenshot 2024-06-25 at 22 41 47" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/ca65de9f-bcab-4079-81e4-e8ab9bc9079c">

**2. Configure the job/project to copy artifacts over to NFS server**
On main dashboard select Manage Jenkins > Configure System menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

- Provide a private key (content of .pem file that we use to connect to NFS server via SSH/Putty)

- Arbitrary name

- Hostname - can be private IP address of our NFS server

- Username - ec2-user (since NFS server is based on EC2 with RHEL 9)

- Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

Result:

<img width="1648" alt="Screenshot 2024-06-25 at 23 21 03" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/dbc1b786-88ae-49f0-8a1d-807af24d647b">

Test the configuration and make sure the connection returns Success. N.B that TCP port 22 on NFS server must be open to receive SSH connections

Result:

<img width="1402" alt="Screenshot 2024-06-25 at 23 21 41" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/6cf86053-a321-4cad-b8d3-4a788eb6b876">

Save the configuration, open your Jenkins job/project configuration page and add another one Post-build Action (Send build artifact over ssh).

Result:

<img width="1139" alt="Screenshot 2024-06-25 at 23 33 31" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/6fc8f372-8453-4fa2-8e83-a774842b2f4f">

Also, Configure it to send all files produced by the build into our previouslys define remote directory In our case we want to copy all files and directories, so we use ** If you want to apply some particular pattern to define which files to send

Result:

<img width="1318" alt="Screenshot 2024-06-25 at 23 34 18" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/b54d207c-8f45-4da3-bc39-df10b5b00d22">

Save this configuration and go ahead, change something in README.MD file in our GitHub Tooling repository

Result:

<img width="1676" alt="Screenshot 2024-06-25 at 23 38 55" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/b8b92e90-4eb2-4297-80cd-030a6bd79577">

The line created previously in the README.md file have been removed

Webhook will trigger a new job

Result:


<img width="1533" alt="Screenshot 2024-06-25 at 23 42 48" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/270f2ae9-0876-4b04-ae8b-40fcf4d442db">

To make sure that the files in /mnt/apps have been updated - connect via SSH to our NFS Server and verify README.MD file

```
ls /mnt/apps
```

Result:

<img width="850" alt="Screenshot 2024-06-25 at 23 47 08" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/cd51e499-c12c-466b-9513-83ae1268e5a9">


```
cat /mnt/apps/README.md
```

Result:

<img width="841" alt="Screenshot 2024-06-25 at 23 48 08" src="https://github.com/sheezylion/automation-with-continuous-integration/assets/142250556/4add51d8-8129-4ba5-8643-10088a0568f6">

If you see the changes you had previously made in your GitHub - the job works as expected.

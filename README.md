# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

Jenkins is an open-source automation server that facilitates the automation of several stages of the software development lifecycle, including application development, testing, and deployment. The technology is server-based. Continuous delivery (CD) and integration (CI) pipelines can be created and managed with Jenkins. The development, testing, and deployment of software applications are automated using CI/CD pipelines. 

**What is Jenkins CI/CD Pipeline?**
Jenkins CI/CD stands for Continuous Integration / Continuous Deployment. In computing, a pipeline is a set of stages or processes linked together to form a processing system. Each stage in the pipeline takes an input, processes it in accordance with a set of rules, and then sends the outputs to the stage that follows. Frequently, the pipeline’s overall output is its final step’s output. like the procedures outlined below

* Test code
* Build Application
* Push Repository
* Deploy to Server

All the steps mentioned above will perform in sequence one after the other if any step/stage get failed it will not move forward to another step/stage until the previous step got a success

**What is Jenkins Continuous Integration (CI)?**
Jenkins Continuous integration means whenever new code is committed to remote repositories like GitHub, GitLab, etc. Continuous Integration (CI) will continuously build, tested, and merged into a shared repository.

**Continuous Deployment**
Continuous Deployment means automating the further stages of the pipeline automatically or manually deploying the application/code to different environments like Dev, Test, and Production. Automating the build is the main component of Continuous Integration and Continuous Deployment.

**Continuous Delivery**
Each and every build that passed all automated tests and was able to be fully automated and delivered into production only required one click of human intervention is called Continuous Delivery.

### STEP1. INSTALL AND CONFIGURE JENKINS SERVER

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it “Jenkins”
2. Install JDK (since Jenkins is a Java-based application)

```
sudo apt update
sudo apt install default-jdk-headless
```
3. Install Jenkins

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```
Make sure Jenkins is up and running

**sudo systemctl status jenkins**

![Screenshot 2023-08-31 145031](https://github.com/opeyemiogungbe/Pbl-project7/assets/136735745/214f2fb4-3a3a-44d5-a37d-a013029c7e66)

From the image above we can see our Jenkins is up and running.

4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

![Screenshot 2023-11-08 083515](https://github.com/opeyemiogungbe/Pbl-project7/assets/136735745/5f481b60-8cd9-418e-98c3-4287850544cd)

5. Perform initial Jenkins setup.

From your browser access http://(Jenkins-Server-Public-IP-Address-or-Public-DNS-Name):8080 

You will be prompted to provide a default admin password etrieve it from your server:

**sudo cat /var/lib/jenkins/secrets/initialAdminPassword**

Then you will be asked which plugins to install – choose suggested plugins options.

![Screenshot 2023-11-08 084218](https://github.com/opeyemiogungbe/Pbl-project7/assets/136735745/8d00288e-569a-47e4-85fe-c6bea89bed18)


Our jenkins is now ready..

### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

  Jenking webhook trigger is a plugin that can receive any HTTP request and trigger a build with the values extracted from the request content with JSONPath or XPath. It can also extract values from the query parameters or headers. The plugin can be used to trigger jobs only if a supplied regular expression matches the extracted variables.

  In this step, we will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable webhooks in your GitHub repository settings

![Screenshot 2023-09-01 084212](https://github.com/opeyemiogungbe/Pbl-project7/assets/136735745/ed864543-3b70-422a-aa93-2d99fc7b34ea)

In the image above, we made use of jenking public ip address to connect to our github webhook to trigger build.

2. Go to Jenkins web console, click “New Item” and create a “Freestyle project”

![Screenshot 2023-09-01 085820](https://github.com/opeyemiogungbe/Pbl-project7/assets/136735745/d1886cb1-74ad-487a-99e3-e5c7a00218f8)


We will connect our github to connect our GitHub repository, we will use our github URL, we will can copy from the repository itself.

![Screenshot 2023-11-08 085543](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/131e1960-c52a-4657-8714-65d5606e752c)

In configuration of our Jenkins freestyle project we will choose Git repository and provide the link to our Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![Screenshot 2023-09-01 085906](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/f2c50cf6-943c-42bd-984d-2e0a76a18cf5)

We will save the configuration and try to run the build. For now we can only do it manually.
Clicking the “Build Now” button, if we have configured everything correctly, the build will be successfull and we will see it under #1

![Screenshot 2023-09-01 090016](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/ff8f9b45-402c-4995-9198-b6458c784164)

If we look at the down left corner of the image above, we will see a green #1. This shows our build was succesful. We just made our very first Jenkins build! But this build does not produce anything and it runs only when we trigger it manually. We are going to fix that and let it run automatically. 

3. Click “Configure” your job/project and add these two configurations

Configure triggering the job from GitHub webhook: 

![Screenshot 2023-11-08 135259](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/86813c96-f719-481e-a403-9520c1dcfda5)

Configure “Post-build Actions” to archive all the files – files resulted from a build are called “artifacts”.

![Screenshot 2023-11-08 135403](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/63c711a7-b82b-446b-8df4-d8671d37add2)

Now,we will go ahead and make some changes in our README.md file in GitHub repository and push the changes to the master branch. We will see that a new build has been launched automatically (by webhook) and we can see its results – artifacts, saved on Jenkins server.

![Screenshot 2023-09-01 091703](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/ecf4e2a5-48a1-400d-9eb1-65ff4f3915ae)

![Screenshot 2023-09-01 092549](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/f32fdfa3-eb87-4897-b441-a55030c3f5c2)

We have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`

### CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH

Step 3 – Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory. We will use a plugin that is called “Publish Over SSH”.

1. Install “Publish Over SSH” plugin.

On main dashboard select “Manage Jenkins” and choose “Manage Plugins” menu item.

On “Available” tab search for “Publish Over SSH” plugin and install it 

![Screenshot 2023-09-02 132414](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/c87492b8-cb2d-40cf-b499-b82c899c8ec2)

2. Configure the job/project to copy artifacts over to NFS server.

On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

* Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
* Arbitrary name
* Hostname – can be private IP address of your NFS server
* Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
* Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

We will test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

![Screenshot 2023-09-02 133619](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/cdd11c88-dad1-4e2a-ad9e-adddae8e7272)

Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”

![Screenshot 2023-09-02 135318](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/95184794-a0f9-4748-8e61-f0a353b7b8a4)

We will configure it to send all files produced by the build into our previously define remote directory. In our case we want to copy all files and directories – so we use **.

![Screenshot 2023-09-02 135651](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/cc517a69-145c-4a0c-9485-90d97e75a38e)

We will save this configuration and change something in README.MD file in our GitHub Tooling repository, Webhook will trigger a new job and in the “Console Output” of the job we will find something like this:

```
SSH: Transferred 25 file(s)
Finished: SUCCESS
```

![Screenshot 2023-09-02 143449](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/0f8c3355-f4f3-4cf1-bab9-20d40b8002e0)

To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check README.MD file

cat /mnt/apps/README.md
if we you see the changes we had previously made in your GitHub – the job works as expected.

![Screenshot 2023-09-02 143644](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/360686b6-f556-4b68-aa4f-e632c356028c)

We can see the changes in our README.md. Our project is now succesful.

Now our architecture looks like this:

![Screenshot 2023-08-29 073738](https://github.com/opeyemiogungbe/Pbl-project9/assets/136735745/1d31881c-3122-4c25-840a-dd52137285b7)



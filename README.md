# Deployment of end-to-end application in node.js using Jenkins CICD with GitHub Integration

---

| &nbsp; | &nbsp; |
| --- | ----------- |
| **Objective** | - Deploy end-to-end application in node.js using Jenkins CICD with GitHub Integration <br>- Trigger Jenkins pipeline automatically once the code is pushed on GitHub |
| **Approach** | - Using Amazon's Elastic Compute Cloud (EC2) for running application on the Amazon Web Services (AWS) infrastructure <br>- Containerize application by creating Dockerfile<br>- Integrate GitHub with Jenkins using Webhook |
| **Impact** | - Jenkins pipeline triggers automatically once the code is pushed on GitHub</br>- Accomplish faster quality releases by automating CI/CD pipelines |
<br>

**Primary Technology:** Github, Docker, Jenkins, aws EC2 service


<br><br>
---

## 1. Creating a Node App
Before we write any CI/CD pipeline we need an application to test and deploy. We are going to build a simple **to-do application** in node.js. Then, create new repository under your GitHub account and name it “node-todo-cicd”.
![1](https://user-images.githubusercontent.com/102405945/211755449-e0c617d8-a9fe-4250-83ad-0ca258a10bbc.png)


## 2. Creating AWS EC2 instance

### 2.1 Creating AWS EC2 instance
After logging into your AWS account, search for EC2
![2](https://user-images.githubusercontent.com/102405945/211756038-f8e88d8f-7e73-4818-974f-e8df7023b5b0.png)
Select **Instances(running)**
![3](https://user-images.githubusercontent.com/102405945/211756073-a00f53a0-d24b-4b9a-a6e7-ba4e4e3d4395.png)
Click **Launch instances**
![4](https://user-images.githubusercontent.com/102405945/211756098-ef0517b6-21f5-4f87-b126-877e4848bf3f.png)
Enter Name and select **Ubuntu**
![5](https://user-images.githubusercontent.com/102405945/211756124-6936130d-84e4-4c9d-8faa-755ff9ddf514.png)
Select **t2.micro** as Instance type and create new key pair to connect to the server
![6](https://user-images.githubusercontent.com/102405945/211756138-1ffe145b-1902-4ab3-99cc-ca2b83688491.png)
Enter **key pair name** and select **RSA** as Key pair type and **.pem** as Private key file format. Then, click **Create key pair**
![7](https://user-images.githubusercontent.com/102405945/211756153-68eb2114-beb3-437c-b7e9-702d32c936fb.png)
Finally, click **Launch instance**
![8](https://user-images.githubusercontent.com/102405945/211756187-90c18d04-b83b-4f6d-b24f-3cb68c3bf28a.png)
![9](https://user-images.githubusercontent.com/102405945/211756212-4c5b0a85-e4aa-4270-b5e0-32cd973915a4.png)

### 2.2 Connecting AWS EC2 instance
Click **Connect** on the top of the screen
![10](https://user-images.githubusercontent.com/102405945/211757625-5b5670e8-ed7d-4f31-9de2-0918a1337ede.png)
Click **Connect**
![11](https://user-images.githubusercontent.com/102405945/211757652-86ebdc4d-1528-4d3b-8160-dddd77b5ed0d.png)


## 3. Installing Jenkins on AWS

### 3.1 Installing Java
Install Java using following commands: <br>
**sudo apt update**<br>
**sudo apt install openjdk-22-jre**<br>
**java -version**<br>
![12](https://user-images.githubusercontent.com/102405945/211758445-cc9c558b-98b1-4dad-bc41-6e1647c4298e.png)
![13](https://user-images.githubusercontent.com/102405945/211758553-a5e8f47e-638a-4a36-bcfa-db6a03ac3c5b.png)
![14](https://user-images.githubusercontent.com/102405945/211758628-299332b3-cfc1-4f4a-a27a-85af87b45817.png)

### 3.2 Installing Jenkins
Install Jenkins using following commands: <br>
**curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \   /usr/share/keyrings/jenkins-keyring.asc > /dev/null **<br>
**echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \   https://pkg.jenkins.io/debian binary/ | sudo tee \   /etc/apt/sources.list.d/jenkins.list > /dev/null**<br>
**sudo apt-get update**<br>
**sudo apt-get install jenkins**<br>
![15](https://user-images.githubusercontent.com/102405945/211760572-4f5ec5b4-f52c-451f-9fe1-028241759235.png)
![16](https://user-images.githubusercontent.com/102405945/211760685-c9b9da1d-d544-428b-b3a5-09e5b72f7f7c.png)

### 3.3 Start Jenkins
Start jenkins using following commands: <br>
**sudo systemctl enable jenkins**<br>
**sudo systemctl start jenkins**<br>
**sudo systemctl status jenkins**<br>
![17](https://user-images.githubusercontent.com/102405945/211760952-c3984654-00de-4c47-8e2f-b1f31a9aba2d.png)

### 3.4 Open port 8080 from AWS Console
Go to Instances. Click on Security tab
![18](https://user-images.githubusercontent.com/102405945/211761473-452237ae-4b38-435d-a665-54de46ad0531.png)
Click on the link below Security groups
![19](https://user-images.githubusercontent.com/102405945/211761517-f85d3794-89ec-42f1-bd68-b8d9f1fe6c7b.png)
Click on **Edit inbound rules**
![20](https://user-images.githubusercontent.com/102405945/211761553-008189c7-b351-4706-829a-accaf4dd02ec.png)
Click on **Add rule** and add port 8080 and select **My IP** and then click **Save rules**
![21](https://user-images.githubusercontent.com/102405945/211761596-7d06f0a1-f2d7-410e-bb44-9e0f5a987ecc.png)
![22](https://user-images.githubusercontent.com/102405945/211761624-4762a332-f20c-41c2-88a0-23e92a5186c7.png)

### 3.5 Unlock Jenkins
Open port 8080 on new tab
![23](https://user-images.githubusercontent.com/102405945/211763309-270c2fe1-db98-43c6-9b39-032affcf4e38.png)
On console, type the command **sudo cat /var/lib/jenkins/secrets/initialAdminPassword** and copy the password
![24](https://user-images.githubusercontent.com/102405945/211763345-85dcd1a5-78db-4770-a7b5-392248f23909.png)
Paste the password in Administrator password to unlock jenkins
![25](https://user-images.githubusercontent.com/102405945/211763367-b2d215be-ea2b-4a45-bb20-2ec3acef41fe.png)
Install suggested plugins
![26](https://user-images.githubusercontent.com/102405945/211763394-0ea3d5af-8c15-466d-8be2-99487cc1d873.png)
![27](https://user-images.githubusercontent.com/102405945/211763441-25d3a088-6119-43b0-a04e-cde33cfaca52.png)
Create First Admin User
![28](https://user-images.githubusercontent.com/102405945/211763485-8449f84f-313a-4478-ab81-bebdf36a808f.png)
Enter Jenkins URL and click **Save and Finish**
![29](https://user-images.githubusercontent.com/102405945/211763529-e7ac31bd-38b6-45ef-ab43-118ff9b93fa3.png)
Finally, Jenkins is ready. Click on the button **Start using Jenkins**
![30](https://user-images.githubusercontent.com/102405945/211763586-17fdc134-9757-4b82-ad67-5eb57fc4ab9c.png)


### 3.5 Connect GitHub and Jenkins
Create new job by clicking **New item**
![32](https://user-images.githubusercontent.com/102405945/211767102-7ba3199c-a108-4a4f-8ebc-4ba25ef1af38.png)
Enter an item name. Select **Freestyle project**. Add description. Select **GitHub project** and add project url
![33](https://user-images.githubusercontent.com/102405945/211767157-ac02438b-86a8-4f66-8056-87be60df0588.png)
Select **Git** as Code Management and add **Repository URL**. Click on **Add** to add key
![34](https://user-images.githubusercontent.com/102405945/211767199-de9358d1-b081-4f8e-9da8-bead2ff93db3.png)
Generate SSH key on console using following commands: <br>
**ssh-keygen** <br>
**cd .ssh** <br>
**cat id_rsa_pub** <br>
Copy the public key
![35](https://user-images.githubusercontent.com/102405945/211767270-54cf0dbd-2fc5-49c3-9fc4-7983eef0b9bb.png)
![36](https://user-images.githubusercontent.com/102405945/211767284-65ecd0ee-27fe-4db5-b0fc-56be725e85bd.png)
![37](https://user-images.githubusercontent.com/102405945/211767299-10ac5fdb-9812-4f87-aeae-3f774757bd4d.png)
Go to GitHub. Click on **Settings**
![38](https://user-images.githubusercontent.com/102405945/211767336-6f0ac758-7f80-432c-b084-924671fcc8fa.png)
Click on **SSH and GPS keys** on the left pane and click on **Add SSH key**
![39](https://user-images.githubusercontent.com/102405945/211767369-dd05d32c-c87c-4637-9056-6d675a01459f.png)
Paste the SSH key and click **Add SSH key**
![40](https://user-images.githubusercontent.com/102405945/211767410-9b2a26c0-779d-4f64-946f-229ebce031ea.png)
Go to Jenkins, and select **SSH Username with private key** in kind
![41](https://user-images.githubusercontent.com/102405945/211767451-530ca1d9-1ea2-49c9-9637-c82a6a415357.png)
On console, enter the command **cat id_rsa** and copy the private key
![42](https://user-images.githubusercontent.com/102405945/211767482-00352640-a688-4001-8a2f-f5d2c3983efa.png)
Paste the private key in jenkins wizard
![43](https://user-images.githubusercontent.com/102405945/211767525-854c8421-a07e-4c04-b235-45be202d1247.png)
Select **ubuntu(This is for github and jenkins integration)** in credentials
![44](https://user-images.githubusercontent.com/102405945/211767548-5aac0cd6-ca4a-46e6-9d46-b7fd71f23dad.png)
Enter ***/master** in Branch Specifier and click **Save**
![45](https://user-images.githubusercontent.com/102405945/211767592-a9aaf324-878b-4716-a2f6-0de181d67b78.png)


### 3.6 Get code in jenkins
In jenkins, click on **Build Now** on the left pane
![46](https://user-images.githubusercontent.com/102405945/211769407-1f79b77a-8f34-4966-9938-d70121f5305d.png)
Now, click on **#1** and select **Console Output** to view the console
![48](https://user-images.githubusercontent.com/102405945/211769445-03122663-f40d-43fc-9e6a-83d545e87e27.png)
![49](https://user-images.githubusercontent.com/102405945/211769487-4ab1a5c4-2c22-4cdc-9462-1ba62cf0bed4.png)
To check whether we got the code on EC2 instance, go to console, and enter the following commands: <br>
**sudo cd /var/lib/jenkins/workspace/todo-node-app** <br>
**ls** <br>
Clearly, the code is present in this directory
![50](https://user-images.githubusercontent.com/102405945/211769557-f8f0f62e-b426-481d-b209-11b09e6f61f1.png)



### 3.7 Run node.js application

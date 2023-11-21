# ToDo-Node-App Project1 8-)
#### In this project we will do CICD pipeline on an AWS EC2 instance using Git and Jenkins. We will make a Nodejs App. 
#### Tool we will need in this project is :-
+ 1.) Docker 
+ 2.) Github 
+ 3.) AWS EC2 
+ 4.) Jenkins
___
# Prerequisites
#### Before starting the project you should have these things in your system :-
+ Account on AWS
+ Git
+ Todo App code (we will use code from this repository : [link with todo-list](https://github.com/sudhajobs0107/todo-list.git)
___
## STEP 1: Launch Instance
+ Create AWS EC2 instance
![Instance](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/instance.PNG)
+ Connect to instance
+ After successfully connecting to the EC2 instnce, it will look like this
![Connect Interface](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/connect%20interface.PNG)
___
## STEP 2: Install Java
+ Use command as follow :-
```
sudo apt update
```
```
sudo apt install openjdk-17-jre
```
+ To check java install or not use command :-
```
java -version
```
___
## STEP 3: Install Jenkins
+ Use command as follow :-
```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```
+ Next command :-
```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
```
sudo apt-get update
```
#### This time you will see jenkins in update :-
![pkg_jenkins](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/pkg_jenkins.PNG)
+ **Next command :-**
```
sudo apt-get install jenkins
```
___

## STEP 4: Start Jenkins
+ Use command as follow :-
```
sudo systemctl start jenkins.service
```
+ Next command :-
```
sudo systemctl status jenkins
```
+ image active_running
+ Now copy **Public IPv4 address:8080** and you will see :- 
+ image jenkins
___
## STEP 5: Unlock Jenkins
+ **Use command as follow :-**
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
+ And we will get our password. Copy and paste it to unlock Jenkins :-
![unlock_jenkins](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/unlock_jenkins.PNG)
#### Now click **Install plugins** 
![jenkins-is-ready](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/install_plugins.PNG)
## **Welcome to Jenkins**
![welcome-to-jenkins](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/welcome-to-jenkins.PNG)

#### Now click on **Create a job** => give name "**todo-node-app**" => select "**Freestyle project**" => click **OK**.
![create-a-job](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/create-a-job.PNG)

#### Now add "**Description**" => selct "**GitHub project**" and **project url**  => Source Code Management "**Git**" and add "**Repository URL**" 
#### Now we have to connect **GitHub** to **server through SSH**, we will create **ssh-keygen** . So for this we go to **EC2 instance** and write command :-
```
ssh-keygen
```

#### And our private key pair will build and it will look like this :-
![keygen](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/keygen.PNG)

## Now write command :-
```
cd .ssh
```
```
ls
```
## It will show like this :-
![show-keygen](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/show-keygen.PNG)

## Now write command :-
```
cat id_rsa
```

## It will show our private key, see in photo :-
+image private-key
## Now write command :-
```
cat id_rsa.pub
```
## It will show our public key, see in photo :-
![public-key](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/public-key.PNG)

#### How to connect Git to EC2 instance?
#### Go to **GitHub** => go to **settings** => **SSH keys** => **new SSH key** => write "**Title**" => in key section **paste the public key** that we generated in **EC2 instance** => click "**add SSH key**". Here what we did, **we give public key to GitHub**. Now **Github have access to connect our instance**.

#### Now go back to **Jenkins** and add "**Credentials**". In credentials add **Kind "SSH Username with private key"** => ID "**github-jenkins**" => username "**ubuntu**" => enter directly "**add private key**" => click on "**add**". "**Save and Build Now**" Here we made **pipeline between GitHub and Jenkins.**
![buildnow](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/build-now.PNG)

#### To check code come on workspace or not use command :-
```
cd /var/lib/jenkins/workspace/todo-node-app
```
![in](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/in.PNG)
## To run this code use command :- 
```
sudo apt install nodejs
```
```
sudo apt install npm
```
```
sudo npm install
```
```
node app.js
```
![running](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/running.PNG)

#### To run our app we have **open port no. 8000**. So for this "**select instance**" => **go to security** => **edit inbound rules** => Add Type "**custom TCP**", Port range "**8000**", Source "**Anywhere**" => **Save rules**.

#### Now copy "**Public IP address:8000**" and paste in another tab and **our app start working**, see in image :-
![ToDo-Node-app](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/ToDo-Node-app.PNG)

#### We have done all things manually, now we have to do these things **automate** through **docker**. So we will make **Dockerfile**, go to instance and write command :-
```
sudo apt install docker.io
```
```
sudo vim Dockerfile
```
#### And we are in Dockerfile. Write code as shown below :-
```
FROM node:12.2.0-alpine
WORKDIR app
COPY . .
RUN npm install
EXPOSE 8000
CMD ["node","app.js"]
```
#### Now we have to build "**Docker Container**" from "**Dockerfile**", for this we use command as shown below :-
```
sudo usermod -a -G docker $USER
```
```
sudo reboot
```
```
docker build . -t todo-node-app
```
#### Now docker container build see in image given below :-
![container](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/container.PNG) 
![container2](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/container2.PNG)

#### Now we **run "Docker Container"** so for this use command :-
```
docker run -d --name todo-node-app -p 8000:8000 todo-node-app
```

#### Our **container start running** see in image given below :-
![running2](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/running2.PNG)

#### If you want to see **docker status** use command :-
 ```
docker ps
```
![docker_status](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/docker_status.PNG)

#### We run our app though docker see in given image below :-
![dockerrun](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/dockerrun.PNG)

#### We did all things through docker manually , now we will do these things automate through **jenkins CICD pipeline**. First kill the running container so for this use command :-
 ```
docker kill (conatainer id)
```

#### Now go to **jenkins** and **configure** and in "**Build Steps**" we will write same steps and automate through jenkins that we wrote steps manually in docker. Steps are as follws :-
 ```
docker build . -t todo-node-app
docker run -d --name todo-node-app -p 8000:8000 todo-node-app
```

#### Save and Build Now. **Our app is build successfull** see in given image below :-
![jenkinsrun](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/jenkinsrun.PNG)

#### Our app is running through Jenkins :-
![app](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/app.PNG)

#### Now we are doing **CICD manually** by **pressing Build Now button** but if we want this **automate like devloper push code on GitHub and Jenkins pipeline run automatically** then first install "**GitHub Integration" plugins** in jenkins. Now go to **GitHub** => **Repo Settings** => **Webhooks** => **Add webhook** => in payload url "**paste jenkins url and github-webhook/** . Now **go to instance** => **security** => **edit inbound rule** => **in port 8080 change source "Anywhere" so that github will access instance** => **change content type to "application/json"** => click "**Add Webhook**". Our webhook added see in image given below :-
![webhook](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/webhook.PNG)

## Why we add webhook? 
## We add webhook **because we have to connect GitHub to Jenkins so if anything happened in GitHub then it triggers and Jenkins pipeline will start automatically**.
### [Emojies](https://github.com/markdown-it/markdown-it-emoji)
#### Now **go to jenkins** and **select job** => **Configure** => **in Build Triggers** tick  "**GitHub hook trigger for GITScm polling**". **Now if we will change code "ToDo-Node-App" to "Sudha's-ToDo-List"**, for this go to **github** => **change code** => **commit** and it **automatically triggers in jenkins** and our app will run see in image given below :-
![final](https://github.com/sudhajobs0107/ToDo-Node-App/blob/master/images/final.PNG)


# Our ToDo-Node-App project is completed :-).

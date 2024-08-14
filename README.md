**JENKINS END TO END CICD Implementation**

**Step 1:** Create a compute engine VM in Google cloud platform

![image](https://github.com/user-attachments/assets/30965368-17de-4a37-ad47-40ec12a62cfc)

**Step 2:** To connect to the VM via Putty from windows , download the puttygen and putty.exe from the 
              https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html 

Open Putty-gen and click on Generate,   copy the key and paste in the cicd-instance under ssh keys , Here iam created key comment gcp-demo and click save private key into  the local

![image](https://github.com/user-attachments/assets/60139ee5-f376-4c74-9419-6418e115fa39)
 

Once key has been added into the cicd-instance 

**Step 3: Open Putty   Session (Give external IP address ) - SSH  Auth  give the ppk file path which was stored under the location  Data  give username (gcp-demo)**

![image](https://github.com/user-attachments/assets/f526aa15-9305-48a6-a5e0-47a60e30d658)

click on Open now we are able to login with the VM via putty

![image](https://github.com/user-attachments/assets/e24a27af-ca0d-440d-82e2-340bf46203eb)

**Step 4: Install Java and jenkins  in the VM**
 $ sudo apt-get update –y
 $ sudo apt install openjdk-17-jre

 ![image](https://github.com/user-attachments/assets/ce8910ca-9952-4264-a4bd-492e81cc9e14)

 **Step 5:** Installing the Jenkins https://pkg.jenkins.io/debian-stable/

** This is the Debian package repository of Jenkins to automate installation and upgrade. To use this repository, first add the key to your system **

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key 

**Then add a Jenkins apt repository entry:
**
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

**Update your local package index, then finally install Jenkins: 
**
sudo apt-get update -y

sudo apt-get install Jenkins

Jenkins –version

Step 6 : Before login to Jenkins create a firewall rule  

![image](https://github.com/user-attachments/assets/81d24372-84af-4720-913a-01bb63d67ab1)

![image](https://github.com/user-attachments/assets/a75f1faa-234d-4409-8aa6-aaa6b15829df)

Add firewall tag  allow-fw (target tag)  to the cicd-instance 
Login to Jenkins : https://<external-ip>:8080  
For our demo ip address is : http://34.85.150.72:8080/

![image](https://github.com/user-attachments/assets/d9a4514f-9bfa-4a7d-93c5-d5180b200198)

**Successfully logged into the Jenkins:**

![image](https://github.com/user-attachments/assets/30ebb6c8-b4c1-45a4-a581-af31b1a44901)

**Install the Docker Pipeline plugin & Sonarqube Scanner plugin in Jenkins:**
•	Log in to Jenkins.
•	Go to Manage Jenkins > Manage Plugins.
•	In the Available tab, search for "Docker Pipeline" "sonarqube scanner"
•	Select the plugin and click the Install button.
•	Restart Jenkins after the plugin is installed

![image](https://github.com/user-attachments/assets/e8b9c62c-485f-4e1f-9c0d-3316b4af0bad)



Wait for the Jenkins to be restarted.
Install Docker Engine on Ubuntu

**Run the following command to uninstall all conflicting packages:**
$ for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
**Install using the apt repository**
# Add Docker's official GPG key:
$ sudo apt-get update
$ sudo apt-get install ca-certificates curl
$ sudo install -m 0755 -d /etc/apt/keyrings
$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
$ sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
$sudo apt-get update

**To Install the latest version:**
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


**To check the docker version:**

![image](https://github.com/user-attachments/assets/14af4215-fa6f-43f5-973d-b4f24055ecd7)

**Grant Jenkins user and Ubuntu user permission to docker deamon.**
sudo su - 
usermod -aG docker jenkins
usermod -aG docker gcp-demo
systemctl restart docker

![image](https://github.com/user-attachments/assets/44a34283-2b51-4a42-8a87-102509e341f8)

**Next Steps
Configure a Sonar Server locally in  the same VM or another instance. Iam installing sonarqube in the same instance with different user **


$ sudo apt install unzip
$ sudo su –
$ adduser sonarqube    (create password for the user)
$ sudo su – sonarqube  (switch to sonarqube user)
sonarqube@cicd-instance:$  wget  https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.5.90363.zip
sonarqube@cicd-instance:$  unzip sonarqube-9.9.5.90363.zip
sonarqube@cicd-instance:$  chmod -R 755 /home/sonarqube/sonarqube-9.9.5.90363
sonarqube@cicd-instance:$  chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.9.5.90363
sonarqube@cicd-instance:$  cd sonarqube-9.9.5.90363/bin/linux-x86-64/
sonarqube@cicd-instance:$  ./sonar.sh start

![image](https://github.com/user-attachments/assets/fbfbea6c-db30-49a9-b665-bf5cb66d4334)

sonarqube start at port number 9000 https://34.85.150.72:9000

By default username and password is same : admin  

![image](https://github.com/user-attachments/assets/b8339897-cf90-4c3a-8b91-60e679b00688)


Change the default password immediately : 

![image](https://github.com/user-attachments/assets/5f327bd2-b50c-4961-87b4-fb2138918a63)

**Generate Token in sonarqube **
Go to sonarqube --> click on my account at right side --> click security --> generate token for Jenkins 

![image](https://github.com/user-attachments/assets/1605185f-f93f-4711-b8af-c65a3b51a2e7)

**Go to Jenkins Url :**

**manage Jenkins --> credentials --> system --> Global Credentials (unrestricted) --> Add credentials **

paste the sonarqube token under the secret text

![image](https://github.com/user-attachments/assets/3f14ee4a-8c37-4d2b-ac5c-71b30d7b6359)













































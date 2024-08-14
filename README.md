# **JENKINS END TO END CICD SPRING BOOT APPLICATION  Implementation**

**Step 1:** **Create a compute engine VM in Google cloud platform**

![image](https://github.com/user-attachments/assets/7f968675-25bb-4649-bf72-585c54e3f2b2)

**Step 2:** To connect to the VM via Putty from windows , download the puttygen and putty.exe from the 
              https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html 

Open Putty-gen and click on Generate,   copy the key and paste in the cicd-instance under ssh keys , Here iam created key comment gcp-demo and click save private key into  the local

![image](https://github.com/user-attachments/assets/fcba238f-0237-48ea-b139-0ecb014c2230)


Once key has been added into the cicd-instance 

**Step 3: Open Putty -->Session (Give external IP address ) --> SSH --> Auth --> give the ppk file path which was stored under the location --> Data --> give username (gcp-demo)**

![image](https://github.com/user-attachments/assets/bd56a6e8-94a4-4009-86ee-9e50f6388731)


click on Open now we are able to login with the VM via putty

![image](https://github.com/user-attachments/assets/5ed5de2f-d228-421f-b424-c5d63c2d05ba)


**Step 4: Install Java and jenkins  in the VM**
 ```
 $ sudo apt-get update –y
 $ sudo apt install openjdk-17-jre

 ![image](https://github.com/user-attachments/assets/f39c80d8-014e-472b-b9c0-97c952f5e679)

 **Step 5:** Installing the Jenkins https://pkg.jenkins.io/debian-stable/

** This is the Debian package repository of Jenkins to automate installation and upgrade. To use this repository, first add the key to your system **

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key 

**Then add a Jenkins apt repository entry:**

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

**Update your local package index, then finally install Jenkins:**

sudo apt-get update -y

sudo apt-get install Jenkins

Jenkins –version
```

Step 6 : Before login to Jenkins create a firewall rule  

![image](https://github.com/user-attachments/assets/fa50039c-06cf-42f7-9eb1-d068cd001d6e)


![image](https://github.com/user-attachments/assets/3a18febc-0e93-492c-be67-7fab531acb44)


Add firewall tag  allow-fw (target tag)  to the cicd-instance 
Login to Jenkins : https://<external-ip>:8080  
For our demo ip address is : http://34.85.150.72:8080/

![image](https://github.com/user-attachments/assets/4b490336-0e97-4289-bca7-bd4cd394abdf)


**Successfully logged into the Jenkins:**

![image](https://github.com/user-attachments/assets/4e9863cd-49a3-4aae-af86-8599d9a8076e)


**Install the Docker Pipeline plugin & Sonarqube Scanner plugin in Jenkins:**
```
Log in to Jenkins.
Go to Manage Jenkins > Manage Plugins.
In the Available tab, search for "Docker Pipeline" "sonarqube scanner"
Select the plugin and click the Install button.
Restart Jenkins after the plugin is installed
```
![image](https://github.com/user-attachments/assets/1921a6a0-3e28-4cf9-b5e5-706f6726b3d4)


Wait for the Jenkins to be restarted.

**Install Docker Engine on Ubuntu**
```  
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
``` 

**To check the docker version:**

![image](https://github.com/user-attachments/assets/c92cf4c9-6320-4341-a10c-12c0d891b9f1)

**Grant Jenkins user and Ubuntu user permission to docker deamon.**
sudo su - 
usermod -aG docker jenkins
usermod -aG docker gcp-demo
systemctl restart docker

![image](https://github.com/user-attachments/assets/3c2aa412-cb37-48d8-9b69-78a510f1c3e0)

Login to Jenkins :

create job name called : **cicd-ultimate --> pipeline** 

![image](https://github.com/user-attachments/assets/f88bc725-46fd-42c0-b633-1a057d511e01)

![image](https://github.com/user-attachments/assets/86d3ec21-5632-4877-a464-9feefddacf05)

**Next Steps Configure a Sonar Server locally in  the same VM or another instance. Iam installing sonarqube in the same instance with different user**
```
$   sudo apt install unzip
$   sudo su –
$   adduser sonarqube    (create password for the user)
$   sudo su – sonarqube  (switch to sonarqube user)
sonarqube@cicd-instance:$  wget  https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.5.90363.zip
sonarqube@cicd-instance:$  unzip sonarqube-9.9.5.90363.zip
sonarqube@cicd-instance:$  chmod -R 755 /home/sonarqube/sonarqube-9.9.5.90363
sonarqube@cicd-instance:$  chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.9.5.90363
sonarqube@cicd-instance:$  cd sonarqube-9.9.5.90363/bin/linux-x86-64/
sonarqube@cicd-instance:$  ./sonar.sh start
```
![image](https://github.com/user-attachments/assets/507a390c-14f1-4ce7-ad47-2dfd4b80bf98)


sonarqube start at port number 9000 https://34.85.150.72:9000 

By default username and password is same : admin  

![image](https://github.com/user-attachments/assets/97654c5c-33a5-4cb5-b725-3847a41e5334)

Change the default password immediately : 

![image](https://github.com/user-attachments/assets/6af80c80-2531-4a71-90af-c1c30080f7a5)

**Generate Token in sonarqube **
Go to sonarqube --> click on my account at right side --> click security --> generate token for Jenkins 

![image](https://github.com/user-attachments/assets/de70721a-483d-4372-a657-d832026bbfcf)

**Go to Jenkins Url :**

**manage Jenkins --> credentials --> system --> Global Credentials (unrestricted) --> Add credentials **

paste the sonarqube token under the secret text

![image](https://github.com/user-attachments/assets/c97b1b85-1a54-412b-8b8e-bea46a315466)

Restart Jenkins : http://34.85.150.72:8080/restart

manage Jenkins   credentials --> system --> Global Credentials (unrestricted) --> Add credentials --> docker-cred (ID)

![image](https://github.com/user-attachments/assets/340755a8-a6a6-4fb7-aacc-58c532eb8b0d)

**Generate a Github token**

Go to github --> click on profile --> settings --> Developer settings --> personal access token --> Tokens (classic ) --> generate a new token 

![image](https://github.com/user-attachments/assets/d768842a-a6b4-4f10-a9d0-5aad8b3b30a5)

Copy the generated token and go to Jenkins --> manage Jenkins --> credentials  --> system --> Global Credentials (unrestricted) --> Add credentials --> kind select (secret text) --> id --> github

![image](https://github.com/user-attachments/assets/638bb1e4-9dcb-4fa1-be91-f66d46d5fa72)

## **CREATING A GKE CLUSTER IN GOOGLE CLOUD VIA CLI**

Activate cloud shell or cloud sdk from the local machine
demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ gcloud config list
[accessibility]
screen_reader = True
[component_manager]
disable_update_check = True
[compute]
gce_metadata_read_timeout_sec = 30
[core]
account = demogcp9@gmail.com
disable_usage_reporting = False
project = prime-hologram-432412-t7
[metrics]
environment = devshellYour active configuration is: [cloudshell-17008]
demogcp9@cloudshell:~ (prime-hologram-432412-t7)$  gcloud auth list
Credentialed Accounts
Enable API’s
demogcp9@cloudshell:~ (prime-hologram-432412-t7)$  gcloud services enable container.googleapis.com compute.googleapis.com

Operation "operations/acf.p2-991667324671-18428e5e-bdb3-467f-a448-cbafc1af198d" finished successfully.
demogcp9@cloudshell:~ (prime-hologram-432412-t7)$  gcloud container clusters create gke-cluster --zone=us-east4-b --machine-type=n2d-standard-2 --num-nodes=1
Default change: VPC-native is the default mode during cluster creation for versions greater than 1.21.0-gke.1500. To create advanced routes based clusters, please pass the `--no-enable-ip-alias` flag
Note: The Kubelet readonly port (10255) is now deprecated. Please update your workloads to use the recommended alternatives. See https://cloud.google.com/kubernetes-engine/docs/how-to/disable-kubelet-readonly-port for ways to check usage and for migration instructions.
Note: Your Pod address range (`--cluster-ipv4-cidr`) can accommodate at most 1008 node(s).
Creating cluster gke-cluster in us-east4-b... Cluster is being health-checked (master is healthy)...done.                                                                  
Created [https://container.googleapis.com/v1/projects/prime-hologram-432412-t7/zones/us-east4-b/clusters/gke-cluster].
To inspect the contents of your cluster, go to: https://console.cloud.google.com/kubernetes/workload_/gcloud/us-east4-b/gke-cluster?project=prime-hologram-432412-t7
kubeconfig entry generated for gke-cluster.
NAME: gke-cluster
LOCATION: us-east4-b
MASTER_VERSION: 1.29.6-gke.1326000
MASTER_IP: 34.150.216.226
MACHINE_TYPE: n2d-standard-2
NODE_VERSION: 1.29.6-gke.1326000
NUM_NODES: 1
STATUS: RUNNING 

### Connect to GKE Cluster :

demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ **gcloud container clusters get-credentials gke-cluster --zone=us-east4-b --project=prime-hologram-432412-t7****
Fetching cluster endpoint and auth data.


**After connected to GKE cluster**  
-->	https://operatorhub.io/operator/argocd-operator 
-->	Install in the GKE cluster or minikube or etc

![image](https://github.com/user-attachments/assets/b97e8052-4932-459b-9585-3f6f31a5ebe8)

demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ kubectl get po -n operators
NAME                                                                                                    READY   STATUS    RESTARTS   AGE
argocd-operator-controller-manager-5dfdc66f8c-nv9kt      1/1          Running          0          19m
Go to the Argo cd  operator documentation 

**https://argocd-operator.readthedocs.io/en/latest/usage/basics/**

demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ **kubectl get po -n operators**
NAME                                                     READY        STATUS         RESTARTS    AGE
argocd-operator-controller-manager-5dfdc66f8c-nv9kt      1/1          Running          0          19m
Go to the Argo cd  operator documentation 

**https://argocd-operator.readthedocs.io/en/latest/usage/basics/**
copy the yaml 

```
apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
  labels:
    example: basic
spec: {}
```

Create a new file in gke cluster  : vim argocd-basic.yaml and paste the above code  

![image](https://github.com/user-attachments/assets/a6034f2c-e82f-4f11-919f-8eaa9207eb6b)

![image](https://github.com/user-attachments/assets/1b325cce-002b-4de0-a6b3-df8a05ce58cf)

All pods in running state then kubectl get svc 

![image](https://github.com/user-attachments/assets/8782c53c-8ef8-4d35-9519-b1d339d5ba04)

demogcp9@cloudshell:~ (prime-hologram-432412-t7)$  **kubectl edit svc example-argocd-server**
service/example-argocd-server edited 
**change type ClusterIP to LoadBalancer**

![image](https://github.com/user-attachments/assets/2bfad994-6618-43e3-a6b1-b422e08306a4)

![image](https://github.com/user-attachments/assets/fb4f6be6-12f1-47b3-a0a2-6ee819c1ee81)

Argocd admin console access : https:// 35.245.105.247:443 

username : admin
for getting  password follow the below steps
demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ **kubectl get secret**
NAME                                   			 TYPE                	DATA   AGE
argocd-secret                           		Opaque            	   5      21m
example-argocd-ca                       		kubernetes.io/tls      3      21m
example-argocd-cluster                  		Opaque              	 1      21m
example-argocd-default-cluster-config   	  Opaque              	 4      21m
example-argocd-redis-initial-password   	  Opaque             	   2      21m
example-argocd-tls                      		kubernetes.io/tls  	   2      21m

demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ **kubectl edit secret example-argocd-cluster**
Edit cancelled, no changes made.
demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ 
demogcp9@cloudshell:~ (prime-hologram-432412-t7)$ **echo S3A2R2lKZzBSUzVBcm1ZUHFNa1Y0ZWJjc1FEMWFoem4= | base64 –d**

Kp6GiJg0RS5ArmYPqMkV4ebcsQD1ahzn

argocd console : username : admin
                 Password : Kp6GiJg0RS5ArmYPqMkV4ebcsQD1ahzn

![image](https://github.com/user-attachments/assets/971a80a3-f9d0-41cb-be29-054928f85532)

![image](https://github.com/user-attachments/assets/83c64c02-8a88-408e-8ee7-eda7f951526e)

then click create

![image](https://github.com/user-attachments/assets/97fdc265-0f70-4ecb-9303-c549cea424bc)

![image](https://github.com/user-attachments/assets/57748b2f-197b-4f7b-b6b4-848430fd76de)

![image](https://github.com/user-attachments/assets/23269603-6c8f-4187-9ad3-005664f5ab0e)

Expose our application outside : http://35.245.121.80/   (EXTERNAL IP for spring-boot-app-service)

**Successfully deployed the application** ❤

![image](https://github.com/user-attachments/assets/190a74c3-7761-442d-a511-98686e0f9b10)


# THANK YOU 🙏❤













































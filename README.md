- [Deploy Mysql](#Deploy-Mysql)

- [Deploy Mysql for Production](#Deploy-Mysql-for-Production)

- [Deploy Java Application](#Deploy-Java-Application)

- [Setup Continuous Deployment with Jenkins](#Setup-Continuous-Deployment-with-Jenkins)

  - [Create Ubuntu Server on DigitalOcean](#Create-Ubuntu-Server-on-DigitalOcean) 

  - [Deploy Jenkins as a Docker Container](#Deploy-Jenkins-as-a-Docker-Container)
 
  - [Install Stage View Plugin](#Install-Stage-View-Plugin)
 
  - [Install Docker In Jenkins](#Install-Docker-In-Jenkins)
 
  - [Install Build tools in Jenkins](#[Install-Build-tools-in-Jenkins)
 
  - [Create Multi Branches Pipelines](#Create-Multi-Branches-Pipelines)
 
  - [Required field of Jenkins](#Required-field-of-Jenkins)
 
  - [Configure build tools in Jenkinsfile](#Configure-build-tools-in-Jenkinsfile)
 
  - [CI Stage](#CI-Stage)
    
    - [Increment Version Dynamically Stage](#Increment-Version-Dynamically-Stage)
   
    - [Build Jar Stage](#Build-Jar-Stage)
   
    - [Build Docker Image Stage](#Build-Docker-Image-Stage)
   
    - [Docker Login](#Docker-Login)
   
    - [Push Docker Image to ECR](#Push-Docker-Image-to-ECR)
   
    - [Make Jenkin commit and push to Repository](#Make-Jenkin-commit-and-push-to-Repository)
   
    - [Configure Webhook to Trigger CI Pipeline Automatically on Every Change](#Configure-Webhook-to-Trigger-CI-Pipeline-Automatically-on-Every-Change)
  
# AWS-EKS 

## Create EKS Cluster . 

#### Connect kubectl locally with EKS Cluster

Eventhough I don't have Worker Nodes yet . I can still can talk to the API Server bcs Control Plane is running

They way to connect is create kubeconfig file for newly created EKS Cluster

Configure Kubectl to connect to EKS Cluster

Step 1: To see AWS configure detail : `aws configure list`

Step 2: To create kubeconfig file locally : `aws eks update-kubeconfig --name <cluster-name>`

-- --name : Connection info for Cluster . Which is the Cluster Name

 - After Created kubeconfig file my Local machine already connected to AWS K8 Cluster. The file will store in `.kube/config`

## Deploy Mysql and phpmyadmin 

#### Deploy Mysql 

To deploy Mysql I will use Helm to make the process more efficent  

Helm is a Package Manager of Kubernetes

Step 1 : Install helm `brew install helm` 

Step 2 : Bitnami is a provider Helm Charts . They also Provide and Maintain MySQL DB Helm chart . To get Bitnami Repo : helm repo add bitnami https://charts.bitnami.com/bitnami

!!! Note : When I execute Helm Command it will execute against the Cluster I connected to

Step 3 : To search for Bitnami Repo : `helm search repo binami/mysql`

Step 4 : Create Mysql values files . So I can override the value that I need for my MySQL 

```
architecture: replication
auth:
  rootPassword: secret-root-pass
  database: my-app-db
  username: my-user
  password: my-pass

# enable init container that changes the owner and group of the persistent volume mountpoint to runAsUser:fsGroup
volumePermissions:
  enabled: true

primary:
  persistence:
    enabled: false

secondary:
  # 1 primary and 2 secondary replicas
  replicaCount: 2
  persistence:
    enabled: false

    # Storage class for EKS volumes
    # storageClass: gp2
    # accessModes: ["ReadWriteOnce"]
```

Step 5: To install MySQL Helm Charts from Bitnami : `helm install <release-name> --values <mysql-helm-value-yaml-file> bitnami/mysql` 

Step 6 : After MySQL DB started . To debug if something wrong with the pods : kubectl logs <pods-name>

Step 7 : I Can also get inside the pod to see mysql pod ENV : kubectl exec -it <pod-name> -- bin/bash Or kubectl describe pod <pod-name>

#### Deploy Mysql for Production 

My values file : 

```
architecture: replication

auth:
  rootPassword: rootpassword  # ✅ use a Kubernetes Secret in real deployments
  database: my-app-db
  username: tim
  password: mypassword     # ✅ use a Kubernetes Secret in real deployments

volumePermissions:
  enabled: true  # ✅ Keep this enabled to ensure volume permissions are correct

primary:
  persistence:
    enabled: true
    storageClass: gp2                     # ✅ Use AWS EBS gp2 or gp3
    accessModes: ["ReadWriteOnce"]
    size: 20Gi                            # ✅ Adjust as per data needs

secondary:
  replicaCount: 2
  persistence:
    enabled: true
    storageClass: gp2
    accessModes: ["ReadWriteOnce"]
    size: 20Gi
```

EKS need certain Perrmission to allow Kubernetes to provision EBS Volumns dynamically when using `storgeClass: gp2 or gp3`

I need to install add-on EBS CSI Driver installed (Must be installed in my Cluster)

  - This driver actually talks to AWS EBS API to Create Volume, Attach them to Pod, Resize, delete them

EBS CSI Driver need an IAM Role with these Permission in AWS-managed policy `AmazonEBSCSIDriverPolicy`

Step 1 : Creates the IAM Role and links it to the Kubernetes service account via an OIDC provider.

```
eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster $CLUSTER_NAME \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --role-name AmazonEKS_EBS_CSI_DriverRole
```

  - Service Account is Application User . As the same way for human User I can link Service Account to Role or ClusterRole with RoleBinding or ClusterRoleBinding, And with binding service account or the Application that is behind that Service account will get Permission that are defined in the Role or Cluster Role

Step 2 : Install the EBS CSI driver as an EKS Add-on and attach the IRSA role

```
eksctl create addon \
  --name aws-ebs-csi-driver \
  --cluster $CLUSTER_NAME \
  --service-account-role-arn arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:role/AmazonEKS_EBS_CSI_DriverRole \
  --force
```

Step 3 : Confirm the EBS CSI driver is installed : `kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver` 

  - I should see something like
    
  ```
  ebs-csi-controller-xxxx        Running
  ebs-csi-node-xxxxx             Running
  ```
**Wrap up**

Every application (pod) runs under a ServiceAccount. If I want it to do something — inside the cluster or outside (like AWS APIs) — I need to give that ServiceAccount the right permissions or attach a role

If I use **Terraform** I can use this : `enable_ebs_csi_driver = true`


## Deploy Java Application 

I have Java Image in my ECR Private Repo

To pull Image From ECR are I need to create Secret Component containe access token and crenditals to my ECR 
 
Configure Deployment to use that Secret using Attribute called `imagePullSecrets` 

#### To Create Secret Component 

```
kubectl create secret docker-registry <my-secrect-name> \
--docker-server=https://565393037799.dkr.ecr.us-west-1.amazonaws.com
--docker-username=AWS
--docker-password=aws ecr get-login-password --region us-west-1
```

#### ENV For Container to connect to DB 

I create Secret Component to store DB_USER, DB_NAME 

```
apiVersion: v1
kind: Secret
metadata: 
  name: java-secret
type: Opaque 
data: 
  DB_USER: dGlt
  DB_NAME: bXktYXBwLWRi
```

And I create Configmap to store DB_URL_SERVER

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: java-configmap
data: 
  database_server: "mysql-primary-0:mysql-primary-headless"
```

I have a `CrashLoopBackOff` means:

 - Your Pod starts → crashes → restarts → crashes again, and this keeps happening in a loop.

 - To know happen I use `kubectl logs <pod-name>`

 - Or I can use `kubectl describe pod <pod-name>` to see events of the pod generating

## Setup Continuous Deployment with Jenkins

#### Create Ubuntu Server on DigitalOcean 

Step 1 : Go to Digital ocean -> Create Droplet -> Choose Region and Capacity -> Create SSH key

Step 2 : To create SSH key :

 - In terminal : `ssh-keygen` . There will be .ssh/id_rsa and .ssh/id_rsa.pub . Then `cat .ssh/id_rsa.pub` take this content and put it into Digital ocean
   
    <img width="600" alt="Screenshot 2025-03-18 at 14 37 03" src="https://github.com/user-attachments/assets/49d7f9df-e2c3-4162-9a5e-7495435fd593" />


#### Deploy Jenkins as a Docker Container

Step 1 : Connect to Server : `ssh root@<IP-address>`

Step 2 : Create Firewall Rule to it

  - Add Custom Port 8080 : This is where Jenkin start . This is where I will expose it 
   
- Add Port 22 : To SSH

Step 3 : Install Docker : `apt install docker.io`

Step 4 : Install Jenkins : `docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts`

 - First 8080 Port : Where the browser access to (The server itself).
   
 - Second 8080 Port : Is the Port of the Container itsefl (Jenkins itself) 
   
 - Port 50000:50000 : This is where Jenkins Master and Worker nodes communicate . Jenkins can actually built and started as a Cluster if I have large Workloads that I am running with Jenkins
   
 - `-d` : Detach Mode. Run the cotainer in the background

 - `-v jenkins_home:/var/jenkins_home`: Mount volumes
        
   - Jenkins is just like Nexus, It will store a lot of data . When I configure Jenkins, Create User, Create Jobs to run, Install Plugin and so ons . All of these will be store as Data
     
   - jenkins_home : This folder doesn't exist yet (Name Volume references) . Docker will create a physical path on the server will store a data with that Name References
     
   - /var/jenkins_home : This is a Actual directory in Cotnainer (Inside Jenkins) that will store data

Step 5 : In the UI . First Access Jenkins will give me a path to get the Password . I will `docker exec -t <container-id> bash` go inside Jenkins and get the password

 - To check Volume that I create : `docker inspect volume jenkins_home`

#### Install Stage View Plugin

This Plugins help me see diffent stage defined in the UI . This mean Build Stage, Test, Deploy will displayed as separate stage in the UI 

Go to Available Plugin -> Stage View

#### Install Docker In Jenkins

Most of scenerio I will need to build Docker Image in Jenkins . That mean I need Docker Command in Jenkins . The way to do that is attaching a volume to Jenkins from the host file

In the Server (Droplet itself) I have Docker command available, I will mount Docker directory from Droplet into a Container as a volume . This will make Docker available inside the container

To do that I first need to kill current Container and create a new : `docker stop <container-id>`

Check the volume : `docker ls volume` . All the data from the container before will be persist in here and I can use that to create a new Container

Start a new container : `docker run -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins:lts`

 - `/var/run/docker.sock:/var/run/docker.sock` : I mount a Docker from Droplet to inside Jenkins
   
Get inside Jenkins as Root : `docker exec -it -u 0 <container_id> bash`

Things need to fix :

 - `apt update` To update Package Manager

 - `apt install curl` : Install `curl`

 - `curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall` . With this Curl command Jenkins container is going to fetch the latest Version of Docker from official size so it can run inside the container, then I will set correct permission the run through the Install

 - Set correct Permission on docker.sock so I can run command inside the container as Jenkins User `chmod 666 /var/run/docker.sock`: docker.sock is a Unix socket file used by Docker daemon to communicate with Docker Client
   
#### Install Build tools in Jenkins

Go to Dashboard -> Manage Jenkins -> Choose Tools -> Add Gradle (Bcs in this project I use Gradle) -> Give it a name is `gradle-8.14`

#### Create Multi Branches Pipelines 

In Jenkins UI -> Dashboard -> New items -> Choose Multi branch Pipelines 

In the Configuration : 

 - Branch Source: Choose Git then add Github url into it

 - Credentials : To create my Github Credential -> Jenkins Dashboard -> Manage Jenkins -> Choose Credentials -> Choose Add Credentials -> Choose Username with Password -> Then give my Github user name and my Github password to it (Github password is a Github token)

 - Behaviors : Choose `Filter by name (with regular expression)` give it value `.*` this mean choose all the branch

 - Build Configuration : By Jenkins -> Script path : Jenkinsfile this mean Jenkins will look for the Jenkins from the Repo url I gave above

Now I have my Multi Branches Pipelines I can start building in the `Jenkinsfile`

#### CI Stage 

CI include : 

 - Increment Version Dynamically Stage
 
 - Testing Stage
 
 - Build Jar Stage

 - Build Docker Image Stage

 - Login to ECR stage

 - Push Image to ECR

#### Required field of Jenkins 

`pipeline` : Must be top level

`agent any`: This build on any available Jenkins Agent . Agent can be a Node, it could be executable on that Node . This is more relevant when I have Jenkins Cluster with Master and Slaves where I have Window Nodes and Linux Nodes ....

`stages` : Where the whole work happen . I have many diffent Stages in Pipeline . Inside Stages I have Stage , Inside Stage I have steps to execute that Stage

#### Configure build tools in Jenkinsfile 

Before any Stages I need to configure build tools  that I use in this project . In this case I use Gradle 

```
pipeline {   
    agent any
    tools {
        gradle 'gradle-8.14'
    }
}
```

#### Increment Version Dynamically Stage 

To automatically Increase Version with Gradle Document (https://theekshanawj.medium.com/gradle-automate-application-version-management-with-gradle-4b97e1df84a3)

I want to be able to automatically increase that version inside my build . So When I commit changes to Jenkins, Jenkins build pipeline basically should increment the version and release a new application . This should all happen immediately .

After followed the Docs I have configured Dynamic Increase Version in Gradle :

 - To Increase Major Version : `gradle majorVersionUpdate`
   
 - To Increase Minor Version : `gradle minorVersionUpdate`
   
 - To Increase Patch Version : `gradle patchVersionUpdate`
   
 - To Realse : `gradlew releaseVersion` # Remove SNAPSHOT

Now In Jenkinsfile I will create a new Stage called `Version Increment Dynamic`

 - In this case I want to increase Patch version : `sh 'gradle patchVersionUpdate'` 

 - To read `version.properties` file in Gradle 
  
   - Install `Pipeline Utility Steps` plugin so I can use `readProperties(file: 'version.properties')`
  
   - `readProperties`, `writeProperties`, `readYaml`, `readJSON`, etc., are all inside this plugin.
  
 - After I have a Version values I can set `IMAGE_NAME` and put it in the ENV for the next Stage use `env.IMAGE_NAME = "${ECR_REPO}:${version['major']}.${version['minor']}.${version['patch']}"`

 - I also created the ENV for ECR_REPO :

   ```
   environment {
      ECR_REPO = "565393037799.dkr.ecr.us-west-1.amazonaws.com/java-app"
    }
   ```
The whole code will look like this : 

```
pipeline {   
    agent any
    tools {
        gradle 'gradle-8.14'
    }

    environment {
      ECR_REPO = "565393037799.dkr.ecr.us-west-1.amazonaws.com/java-app"
    }

    stages {
        stage("Version Increment Dynamic"){
            steps {
                script {
                    echo 'Increase Patch Version ....'

                    sh 'gradle patchVersionUpdate'

                    def version = readProperties(file: 'version.properties')

                    env.IMAGE_NAME = "${ECR_REPO}:${version['major']}.${version['minor']}.${version['patch']}"

                    echo "${env.IMAGE_NAME}"
                }
            }
        }
    }
} 
```

#### Build Jar Stage 

To build Gradle jar : `gradle clean build` 

 - I want to clean the previous stage and then build new one

The code will look like this : 

```
stage("Build Jar") {
  steps {
    script {
      echo "Build Gradle Jar ...."

      sh 'gradle clean build'
    }
  }
}
```

#### Build Docker Image Stage 

I have Dockefile created in my Repository 

```
FROM openjdk:17.0.2-jdk
EXPOSE 8080
RUN mkdir /opt/app
COPY build/libs/bootcamp-docker-java-mysql-project-1.0-SNAPSHOT.jar /opt/app
WORKDIR /opt/app
CMD ["java", "-jar", "bootcamp-docker-java-mysql-project-1.0-SNAPSHOT.jar"]
```

But I want my Dockerfile to read Version dynamically so I will put a `*` in a version like this : 

```
FROM openjdk:17.0.2-jdk
EXPOSE 8080
RUN mkdir /opt/app
COPY build/libs/bootcamp-docker-java-mysql-project-*-SNAPSHOT.jar /opt/app
WORKDIR /opt/app

CMD java -jar bootcamp-docker-java-mysql-project-*-SNAPSHOT.jar
```

To build docker Image : `docker build -t <ecr-repo>/<app-name>:<app-version>`

  - I need to tag ECR repo endpoint bcs When I want to push docker image into ECR, Docker know to endpoint that it need to push to image to

  - In the `Version Increment Dynamic Stage` I already have a IMAGE_NAME with a ECR repo tags I can use it as a ENV

The whole code will look like this :

```
stage("Build Docker Image") {
  steps {
    script {
      echo "Build Docker Image"

      sh "docker build -t ${env.IMAGE_NAME} ."
    }
  }
}
```

#### Docker Login 

I want to login to ECR for Jenkins to able to pull image from there .

I need to configure AWS ECR credentials on in Jenkins : 

 - To get AWS ECR Password : `aws ecr get-login-password --region us-west-1`

 - Username would be : `AWS`

 - Now in Jenkins I will go to Dashboard Credentials and choose Username with Password

In order to use those credentials I will use `withCredentials([]){}` plugin to pull Username and Password from Credential

The Code would look like this : 

```
stage("Login to ECR") {
  steps {
    script {
      withCredentials([
        usernamePassword(credentialsId: 'AWS_Credential', usernameVariable: 'USER', passwordVariable: 'PWD')
      ]){
        sh "echo ${PWD} | docker login --username ${USER} --password-stdin 565393037799.dkr.ecr.us-west-1.amazonaws.com"

        echo "Login successfully"
      }
    }
  }
}
```

#### Push Docker Image to ECR 

In order to push Docker Image to ECR I must login to ECR successfully . That mean the previous Stage must success before this Stage 

The Code will look like this :

```
stage("Push Docker Image to ECR") {
  steps {
    script {
      sh "docker push ${IMAGE_NAME}"
    }
  }
}
```

#### Make Jenkin commit and push to Repository

Everytime pipeline run in Jenkins, it will create a new Image Version, the `versions.properties` in my Gradle project automatically increase, I want to make Jenkin commit and push to the Repository, so when my teammate I want use it, first they have to pull the newest code 

I will add the Git commit Stage :

 - I need credentials to login to Github . Now in Jenkins I will go to Dashboard Credentials and choose Username with Password 

 - In order to use those credentials I will use `withCredentials([]){}` plugin to pull Username and Password from Credential

The code will look like this : 

```
stage("Commit to Git") {
  steps {
    script {
      withCredentials([
        usernamePassword(credentialsId: 'Github_Credential', usernameVariable: 'USER', passwordVariable: 'PWD')
      ]){
        // To set configuration that kept in .git folder and global configuration in git .
        // I want to set git config Global I can put a flag --global
        sh 'git config --global user.email "jenkin@gmail.com"' // If there is no User Email at all, Jenkin will complain when commiting changes . It will say there is no email that was detected to attach to as a metadata to that commit
        sh 'git config --global user.name "Jenkins"'
        
        // Set Origin access
        sh "git remote set-url origin http://${USER}:${PASSWORD}@github.com/ManhTrinhNguyen/AWS-EKS-exercise.git"

        sh "git add ."
        sh 'git commit -m "ci: version bump"'
        sh 'git push origin HEAD:main'
      }
    }
  }
}
```

When Jenkins check outs up to date code in order to start a pipeline it doesn't check out the Branch, it checkout the commit hash (the last commit from that branch). That is the reason why I need to do `sh 'git push origin HEAD:<job-branch>'`. So it saying that push all the commits that we have made inside this commit Branch inside this Jenkin Job.

#### Configure Webhook to Trigger CI Pipeline Automatically on Every Change

##### This way for single Pipeline in Github

I want to trigger build automatically whenever commit is push to Repository 

Other ways to trigger build scheduling. For example I want this build to run every 2 hours 

Go to Github repo -> Settings -> Webhooks -> Click Add Webhooks -> Payload URL : `http://<my-jenkins-domain>/github-webhook/` -> Content type: application/json -> Choose just push event -> Save Webhook 

##### This way for multiple Branches Pipeline 

I need a Plugin call Multibranch Scan Webhook Trigger 

Once installed I have `Scan by Webhook` in Multibranch Configuration -> Choose Scan by Webhook, then I have Trigger Token (This is a token that I can name whatever I want this Token will be use for the communication between Gitlab and Jenkin, or Github and Jenkins ...)

To use this token I will go to Github Repo -> Choose Webhook (Webhook is basically same Integration above) . 

The way it work is It will tell Github to send the notifications on specific URL using that token, and when Jenkins receive that a request it will trigger multibranch pipeline which has scan by webhook configured for that specific token . I don't need secret token 
 












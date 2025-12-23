- [CI/CD diagram overview](#CI/CD-diagram-overview)

- [EKS Cluster Diagram Overview](#EKS-Cluster-Diagram-Overview)

- [Project Overview](#Project-Overview)

- [Create EKS Cluster](#Create-EKS-Cluster)
  
- [Deploy Mysql](#Deploy-Mysql)

- [Deploy Mysql for Production](#Deploy-Mysql-for-Production)

- [Use Terraform to enable EBS CSI Driver](#Use-Terraform-to-enable-EBS-CSI-Driver)

- [MySQL EKS Dependency](#MySQL-EKS-Dependency)

- [Deploy Java Application](#Deploy-Java-Application)

  - [Kubernetes Best Practice](#Kubernetes-Best-Practice)
 
- [Deploy Ingress Controller by using Helm](#Deploy-Ingress-Controller-by-using-Helm)

- [Define Ingress Rule](#Define-Ingress-Rule)

- [Create Helm chart for Java App](#Create-Helm-chart-for-Java-App)

  - [To create Helm chart](#To-create-Helm-chart)
 
  - [Create Deployment Yaml file](#Create-Deployment-Yaml-file)

    - [Use Range to dynamically get ENV data](#Use-Range-to-dynamically-get-ENV-data)
   
  - [Create Service YAML file](#Create-Service-YAML-file)
 
  - [Create ConfigMap Yaml File](#Create-ConfigMap-Yaml-File)
 
  - [Create Secret Yaml File](#Create-Secret-Yaml-File)
 
  - [Create Ingress Yaml File](#Create-Ingress-Yaml-File)
 
  - [Create Values file](#Create-Values-file)
 
  - [Deploy my Helm Chart](#Deploy-my-Helm-Chart)
 
  - [Deploy with Helmfile](#Deploy-with-Helmfile)

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
   
    - [Ignore Jenkins Commit for Jenkins Pipeline Trigger](#Ignore-Jenkins-Commit-for-Jenkins-Pipeline-Trigger)
   
  - [CD Stage](#CD-Stage)
 
    - [Install kubectl inside Jenkins Container](#Install-kubectl-inside-Jenkins-Container)
   
    - [Install aws-iam-authenticator tool inside Jenkins Container](#Install-aws-iam-authenticator-tool-inside-Jenkins-Container)

    - [Create Kubeconfig file to connect to EKS Cluster](#Create-Kubeconfig-file-to-connect-to-EKS-Cluster)
   
    - [Create AWS Credentials](#Create-AWS-Credentials)

    - [Add AWS credentials on Jenkins for AWS account authentication](Add-AWS-credentials-on-Jenkins-for-AWS-account-authentication)
 
    - [Create Secret Component AWS ECR Credentials](#Create-Secret-Component-AWS-ECR-Credentials)
   
    - [Configure Kubernetes Deployment and Service Yaml files](#Configure-Kubernetes-Deployment-and-Service-Yaml-files)
   
    - [Create Deployment Stage in Jenkinsfile](#Create-Deployment-Stage-in-Jenkinsfile)
   
    - [Create Deployment Stage and Deploy with HelmChart in Jenkinsfile](#Create-Deployment-Stage-and-Deploy-with-HelmChart-in-Jenkinsfile)

- [Configure Autoscaling for EKS Cluster](#Configure-Autoscaling-for-EKS-Cluster)

  - [Create custom Autoscaler Policy for my NodeGroup](#Create-custom-Autoscaler-Policy-for-my-NodeGroup)
 
  - [Create OIDC Provider](#Create-OIDC-Provider)
 
  - [Create an IAM role for your service accounts in the console](#Create-an-IAM-role-for-your-service-accounts-in-the-console)
 
  - [Configure Tags on Autoscaling Group . Automate created by AWS](#Configure-Tags-on-Autoscaling-Group-Automate-created-by-AWS)
 
  - [Deploy Cluster Autoscaler](#Deploy-Cluster-Autoscaler)
 
- [Create a Shared Library](#Create-a-Shared-Library)

  - [Structure of Jenkins Share Lib](#Structure-of-Jenkins-Share-Lib)
    
  - [Make Share Library Available](#Make-Share-Library-Available)
 
  - [Project Scoped Share Library](#Project-Scoped-Share-Library)
 
  - [Use Share Library in Jenkinfile](#Use-Share-Library-in-Jenkinfile)
 
  - [Increment Gradle Version](#Increment-Gradle-Version)
 
  - [Build Gradle Jar](#Build-Gradle-Jar)
 
  - [Build Docker Image](#Build-Docker-Image)
 
  - [Docker Login to ECR](#Docker-Login-to-ECR)
 
  - [Push Docker Image To ECR](#Push-Docker-Image-To-ECR)
 
  - [Commit to Git Repo](#Commit-to-Git-Repo)
 
  - [Deploy with Kubernetes](#Deploy-with-Kubernetes)
  
# AWS-EKS 

## CI/CD diagram overview

<img width="900" height="582" alt="Screenshot 2025-12-23 at 12 21 34" src="https://github.com/user-attachments/assets/f79ad835-817f-4572-8204-638635fd25a2" />

## EKS Cluster Diagram Overview 

<img width="1242" height="612" alt="Screenshot 2025-12-23 at 13 15 26" src="https://github.com/user-attachments/assets/73b546bf-8429-4621-a786-60e546858372" />

## Project Overview

This project demonstrates a **complete CI/CD pipeline** integrated with **AWS EKS (Elastic Kubernetes Service)**, **Auto-Scaling**, **Terraform**, **Jenkins**, and **Docker**. It showcases how to automate everything from infrastructure provisioning to containerized application deployment using scalable cloud-native architecture.

#### 🎯 Objectives

- Provision EKS cluster and infrastructure using Terraform (VPC, Subnets, Security Groups, IAM, ECR).
- Implement a multi-branch Jenkins pipeline for CI/CD.
- Automatically build and push Docker images to AWS ECR.
- Deploy to EKS using dynamic Helm chart or kubectl apply.
- Enable Kubernetes Auto Scaling (Node Group + HPA).

#### 🔧 Tech Stack

| Category               | Tools & Services                                 |
|------------------------|--------------------------------------------------|
| Infrastructure as Code | Terraform, AWS (EKS, EC2, IAM, S3, ECR)          |
| CI/CD                  | Jenkins (Pipeline + Shared Libraries)            |
| Containerization       | Docker, Kubernetes                               |
| App Build Tool         | Gradle                                           |
| Secrets Management     | Jenkins Credentials, environment injection       |
| Monitoring             | Horizontal Pod Autoscaler (HPA), Cluster Autoscaler |
| GitOps Trigger         | Webhooks & Multibranch Pipeline                  |


#### Key Features

✅ Terraform modules for VPC, EKS, and IAM roles

✅ Remote backend (S3) with state locking and versioning

✅ Jenkins pipeline with dynamic versioning via Gradle

✅ Secure image push to ECR using Jenkins credentials

✅ Auto Scaling enabled with Kubernetes metrics

✅ Multi-branch pipeline with webhook triggers

✅ Post-deployment container orchestration on EKS


## Create EKS Cluster . 

I have created EKS Cluster by using Terraform in this Repository (https://github.com/ManhTrinhNguyen/Terraform-Exercise) 

#### Connect kubectl locally with EKS Cluster

Eventhough I don't have Worker Nodes yet . I can still can talk to the API Server bcs Control Plane is running

They way to connect is create kubeconfig file for newly created EKS Cluster

Configure Kubectl to connect to EKS Cluster

Step 1: To see AWS configure detail : `aws configure list`

Step 2: To create kubeconfig file locally : `aws eks update-kubeconfig --name <cluster-name>`

-- --name : Connection info for Cluster . Which is the Cluster Name

 - After Created kubeconfig file my Local machine already connected to AWS K8 Cluster. The file will store in `.kube/config`


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

#### Use Terraform to enable EBS CSI Driver

Since K8s version 1.23 an additional driver is required to provision K8s storage in AWS. K8s volumes attach to cloud platform's storage - for AWS this means they attach to EBS volumes. The EBS CSI driver is responsible for handling EBS storage tasks and is not installed by default so without the installation of this driver, K8s volumes cannot be attached to storage in AWS.

Processes on the node group nodes are responsible for creating and attaching these volumes. Because of that, we need to add a permissions policy to the node group so it can request these changes through AWS - this is defined as a managed AWS policy called: `AmazonEBSCSIDriverPolicy`, which we are attaching to the node groups.

So the following 2 code snippets must be added to your EKS Terraform file to make sure EBS CSI driver is activated and the node group nodes have the needed permissions:

```
# 1. Including the add-on as part of EKS module:

cluster_addons = {
    aws-ebs-csi-driver = {}
}

# 2. Adding associated permissions as part of node group configuration:

iam_role_additional_policies = {
    AmazonEBSCSIDriverPolicy = "arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy"
}
```
#### MySQL EKS Dependency

An additional dependency is also required to be defined in your MySQL Terraform configuration. Use the following to ensure that Terraform waits for the EKS cluster to be fully created before provisioning dependent resources

```
data "aws_eks_cluster" "cluster" {
  name = module.eks.cluster_name
  depends_on = [module.eks.cluster_name]
}

data "aws_eks_cluster_auth" "cluster" {
  name = module.eks.cluster_name
  depends_on = [module.eks.cluster_name]
}
```

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


### Kubernetes Best Practice

**Best Practice 1: Pinned (Tag) Version for each Container Image**

Define specific version for Image that I use inside the Pod

If I don't specify the version tag . K8s automatically pulled the latest version . That mean the new Image get built and push to the Repo the Pod will restart and pull the latest version . It might cause break current App, unpridictable, Don't know what version running on my Cluster 

**Best Practice 2: Liveness Probe for each container**

<img width="600" alt="Screenshot 2025-02-28 at 12 38 44" src="https://github.com/user-attachments/assets/394985be-0327-4bdf-aa9a-096e019ef3db" />

Kubernetes has a intelligent managing its resources . Restart pod when it crashed 

But what if the container inside the pod crashed or stucked in the loop ? 

To let Kubernetes know wheather the Application inside the pod is running : Perform Health check with `Liveness Probe` . 

With `Liveness Probe` Kubernetes will restart the Pod if the Application has issues 

Bcs I don't have http `/heath` route in my App I can do `tcpSocket` checks if the port is open (i.e., app is still accepting TCP connections).

```
livenessProbe:
  tcpSocket:
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

**Best Practice 3 : Readness Probe for each container**

K8s manage its resources but not Container . Liveness Probe is the way to check that the Container running or crashing

LivenessProbe is check while the container running . RedinessProbe is check during it start up

```
readinessProbe:
  tcpSocket:
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10 
```

**Best Practice 4 : Resources Limit for each container**

Why Resources Limit important ?

What if Applications need more Resources ? What if when the Application is running need more Resources than Requested ? Bcs if 1 Application can have too much data that it need to load into memory or Application has a bug inside with some infinite loop and start consuming all the CPU available on that Node

If not limited , container could take all the Resources of that Node

  ----Configure resources limits for each container----

!!! NOTE : If I put values larger than my biggest Node resources, my Pod will never be schedule

```
resources:
  limits:
    cpu: 100m
    memory: 200Mi
```

**Best Practice 5 : Resources Request for each container**

I need to define for each container something called resource requests . Some Application may need more CPU or RAM than other

To make sure my Application Container has enough resources to run inside the Pod , I should define Resources Request for that Container

m = milicores

Mi = mebibyte

**Best Practice 6: Don't Expose NodePort**

Even Though using NodePort super easy bcs it expose Cluster Security Risk . Bcs it opened ports on each Worker Node . So have multiple entry point in the cluster . So 
I increase the attack surface

The Best Pratice is only use Internal Services (Cluster IP) and have 1 only 1 entry point to that Service . And ideally that entry point should be sit outside the cluster on a seperate server

Intead of using NodePort I can use LoadBalancer Type which use the CLoud platform's Load balancer to create an external single entry point for the cluster

So Load Balancer will get all the request from Browser or from external resources to Application

And the Alternative I can use Ingress Controller to direct traffice to internal Services 

**Best Practice 7: More than 1 Replica for Deployment**

If 1 Pod crashes, my application is not accessible until new Pods restart !

By Increasing Replicas, make sure even if 1 replica die Application alway remain available

Always Available | No downtime for users

**Best Practice 8: More than 1 Worker Node in the Cluster**

Alway use more than 1 Worker Node
If something happen to a Node all my Application are gone, stopped and not accessible

Especially if i want to run multiple Replica of my Pods, ideally I want each replica to run on the different Node and not all the Replicas run on the same Node . That doesn't give me that much backup if something happen to Node itself

  ----Some reason it happen----
Server crashes
Server reboot bcs of an update
Server maintance
Server Broken 

**Best Practice 9 : Using Label**

Use labels for all Resources
Labeling all my Kubernetes Component

Labels are key value pair

Attach to K8 component like Pod, Service, Deployment ...

Labeling Component is custom identifier for my Component .

Should be meaningful and relevant to user

Label to Group Pods base on Application they belong to and then refencing them in Service Component

Best Practice is to label all Resources 

**Best Practice 10 : User Namepsaces to isolate resources**

Logicly grouping Application and other component into namespace make manging my cluster way easier compared to just throwing the Pod and Service everywhere in the Cluster 

Also For defining diffent priviledges inside the cluster base on the Namespace | I can have multiple team working on the same cluster but each one working on different Namspace

----Namespace Permission----

Role 

RoleBinding 

And This totally up to the team how they decide to group and separate application in namespaces . I can have 1 Microservice per Namespace as well 

**Best Practice 11 : Ensure Image are free vulnerablility**

This can happen when I using library or tools that have some vulnerabiliti for my Application inside the container

Mannually scan vulnerability on Images

Or Automated scan on the Build Pipelines

**Best Practice 12 : No Root access for Container**

Making sure that I don't have container running in my Cluster that have root access capability

This exposed security risk bcs a container with root access can access more resources and do much more on the host where it is running

If the container is hacked, much more damage can be done

So best practice is having none Root user in the container

Most of officical Images Do Not use root user however if I use non-official third party Images . Alway a good practice to check

**Best Practice 13 : Update K8 to latest version**

Important Security Fixed

Bug fixed

----Update K8 Version Node by Node----

To avoild Application downtime 

## Define Ingress Rule

Ingress is an abstract Component in the Cluster . It provide the Routing rules http/https to manage the redirection . Manage which request redirect to which Pod

Create Ingress yaml 

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: java-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: 45-79-231-7.ip.linodeusercontent.com # I use this for host because I don't have a domain name . For test purposes
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: java-app-service
            port:
              number: 8080
```

## Deploy Ingress Controller by using Helm

Ingress controller is another Component in the Cluster to evaluate all the Rules that I defined in the Cluster . That way to manage the Redirection

Ingress Controller use cloud native in the background . So whenever I installed Ingress Controller my Cloud Loadbalancer automatic created

Step 1 : Install helm : `brew install helm`

Step 2 : Add Nginx Repo : `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`

Step 3 : Install Ingress controller from Nginx Repo : `helm install <release-name> ingress-nginx/ingress-nginx --set controller.publishService.enabled=true`

 - `--set controller.publishService.enabled=true` : This attribute to make sure that we are automatically allocated the Public IP for my Ingress Address to use with Nginx

Step 4 : To check Ingress Controller is running : 

 - `kubectl get pods` to see the Ingress controller pod

 - `kubectl get svc` to see Ingress Controller Services

##  Create Helm chart for Java App

I will create new branch for this : `git checkout -b Create-Helm-Chart-For-Java-App`

Helm Chart is a bundle of Yaml file

When I deploy an App with those Configuration Yaml file I can bundle it for the next use 

I will create `charts` folder : `mkdir charts`

#### To create Helm chart 

Inside `charts` folder : `helm create <helm-chart-name>`

#### Create Deployment Yaml file 

Instead of hardcode Values I use syntax for dynamically Values : `{{.Values.<name-of-the-value>}}`

#### Use Range to dynamically get ENV data

For 1 single ENV I can have container ENV like this: 

```
env:
- name : {{ .Values.containerEnvVar.key }}
  value : {{ .Values.containerEnvVar.value }}
```

For working with List of something . In this case ENV, Helm has built function call `Range`

`Range` is basically loop through a List give me a Element one by one

```
env: 
# Loop through Regular ENV data 
{{- range $key, $value := .Values.regularENV }} ## .Values.regularENV This is list of ENV data I defined in `Values` file
- name: {{ $key }}
  value: {{ $value | quote }}
{{- end }}
```

Loop through Secrect and Configmap data 

```
# Loop through Secret
env:
{{- range $key, $value := .Value.secretData }}
- name: {{ $key }}
  valueFrom:
    secretKeyRef:
      name: {{ $.Values.secretName }}
      key: {{ $key }}
{{- end}}

# Loop through Configmap 
{{- range $key, $value := .Value.configData }}
- name: {{ $key }}
  valueFrom:
    configMapKeyRef:
      name: {{ $.Values.configMapName }}
      key: {{ $key }}
{{- end }}
```

!!! NOTE : Inside the `secretKeyRef` at the `name: {{name: $.Values.secretName }}` level . The `$` is for global scope . Bcs if I don't define `$` the scope would be inside the `.Value.secretData` . The same for `configMapKeyRef`

The values file should look like this : 

```
secretName: java-secret
secretData: 
  DB_USER: dGlt
  DB_NAME: bXlfZGF0YWJhc2U=
  DB_PWD: cGFzc3dvcmQ=

configName: java-config 
configData: 
  DB_SERVER: mysql-primary-0.mysql-primary-headless
```

My Deployment YAML file would look like this : 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Values.AppName }}
  labels:
    app: {{ .Values.AppName }}
spec: 
  replicas: {{ .Values.ReplicasCount }} 
  selector:
    matchLabels:
      app: {{ .Values.AppName }}
  template:
    metadata:
      labels: 
        app: {{ .Values.AppName }}
    spec: 
      imagePullSecrets: 
      - name: {{ .Values.ImagePullSecretName }}
      containers:
      - name: {{ .Values.AppName }}
        image: {{ .Values.ImageName }}
        imagePullPolicy: Always
        ports:
        - containerPort: {{ .Values.ContainerPort }}
        livenessProbe:
          tcpSocket:
            port: {{ .Values.ContainerPort }}
          initialDelaySeconds: {{ .Values.InitialDelaySeconds }}
          periodSeconds: {{ .Values.PeriodSeconds }}
        readinessProbe:
          tcpSocket:
            port: {{ .Values.ContainerPort }}
          initialDelaySeconds: {{ .Values.InitialDelaySeconds }}
          periodSeconds: {{ .Values.PeriodSeconds }}
        resources:
          requests:
            cpu: {{ .Values.RequestCPU }}
            memory: {{ .Values.RequestMemory }}
          limits:
            cpu: {{ .Values.LimitCPU }}
            memory: {{ .Values.LimitMemory }}
        env: 
        # Loop through Regular ENV data 
        {{- range $key, $value := .Values.regularENV }}
        - name: {{ $key }}
          value: {{ $value | quote }}
        {{- end }}

        # Loop through Secret
        {{- range $key, $value := .Value.secretData }}
        - name: {{ $key }}
          valueFrom:
            secretKeyRef:
              name: {{ $.Values.secretName }}
              key: {{ $key }}
        {{- end}}

        # Loop through Configmap 
        {{- range $key, $value := .Value.configData }}
        - name: {{ $key }}
          valueFrom:
            configMapKeyRef:
              name: {{ $.Values.configMapName }}
              key: {{ $key }}
        {{- end }}
```

#### Create Service YAML file

```
apiVersion: v1
kind: Service
metadata: 
  name: {{ .Values.ServiceName }}
spec:
  selector:
    app: {{ .Values.AppName }}
  ports:
  - protocol: TCP
    port: {{ .Values.ServicePort }}
    targetPort: {{ .Values.ContainerPort }}
```

#### Create ConfigMap Yaml File 

I also use `Range` to loop the List of ConfigMap Data. In case I have multiple Data 

```
apiVersion: v1
kind: ConfigMap
metadata: 
  name: {{ $.Values.configMapName }}
data:
  {{- range $key, $value := .Value.configData }}
  {{ $key }}: {{ $value | quote }}
  {{- end }}
```

#### Create Secret Yaml File 

I also use `Range` to loop the List of Secret Data. In case I have multiple Data 

```
apiVersion: v1
kind: Secret
metadata: 
  name: {{ $.Values.secretName }}
type: Opaque
data:
  {{-range $key, $value := .Value.secretData}}
  {{ $key }}: {{ $value }}
  {{- end }}
```

#### Create Ingress Yaml File 

```
apiVersion: networking.k8s.io/v1
kind: Ingress 
metadata: 
  name: {{ .Values.ingressName }}
spec:
  ingressClassName: nginx 
  rules: 
  - host: {{ .Values.ingressHost }}
    http: 
      paths: 
      - path: / 
        pathType: Prefix
        backend:
          service:
            name: {{ .Values.ServiceName }}
            port:
              number: {{ .Values.ServicePort }}
```

#### Create Values file 

Places to define default value is in `values.yaml`

What if I want to set the actual valid value instead of default one ? I will using the custom value file

I will create my own values.yaml file usally outside the Chart itself

For example : for emailService Mircro I will create `emailservce-values.yaml`

From Default values file to valid `emailservice` value file

This `values.yaml` is just for reference . I will create another Value file that have correct value to override this 

```
# Deployment and Service
AppName: java-app
ServiceName: java-app-service
ReplicasCount: 2
ImagePullSecretName: registry-name
ImageName: image
ContainerPort: 8080
ServicePort: 8080

InitialDelaySeconds: 5
PeriodSeconds: 5

RequestCPU: 100m
RequestMemory: 64Mi
LimitCPU: 100m
LimitMemory: 200Mi

regularENV: {}

secretName: java-secret
secretData: {}

configMapName: java-config 
configData: {}

ingressName: java-ingress
ingressHost: hostURL
```

#### Deploy my Helm Chart

To validate that my template Yaml file and the values that I define are correct : `helm template -f <value-file> <name-of-helm-chart>` . This give me a nice preview from all the values sources of that value-file

The way it work in the background is : When Helm evaluate a Chart, It will send all the template files that are defined in the templates Directory through Helm's template rendering engine, Engine will then replace all the Variable or Placeholder in those Template File with the actual Values from the Values resources which are User Provided Values File or --set option (For singular value), Helm will then collect the result of those templates and send those file to Kubernetes when I execute `helm install` command

 Another way to reviewing my manifest file is : `helm install --dry-run <redis-values-file> <release-name> <chart-file>`, Check generated manifest without install the chart

Now I can install Helm Chart : `helm install -f <acutal-values-yaml-file> <application-name> <chart-name> --namespace <namespace>`

#### Deploy with Helmfile

Helmfile help me manage and deploy multiple Helm Charts

If you have:

 - Multiple services (apps, databases, monitoring tools, etc.)

 - Multiple environments (dev, staging, prod)

 - Complex values.yaml overrides

 - Secrets and configuration scattered across teams

Install helmfile on MacOS: `brew install helmfile`

After helmfile install I can use helmfile command to update the cluster or sync the cluster with whatever I have declare in our Helmfile

`helmfile sync`: Preparing all the releases . It will then compare the actual State in the cluster with the desired state that I configured in the Helmfile. Base on that it will plan what need to be install and deployed in the cluster to give me a desired State

`helmfile list`: Which show me at any point the currently installed released that Helmfile manage for me

`helmfile destroy`: Uninstall Release

My helmfile would look like : 

```
releases:
- name: java-gradle-app
  chart: charts/java-gradle-deployment
  values:
    - values/java-gradle-values.yaml
```

If I have multiple charts It would be a good practice to deploy Helm Charts by using helmfile

----

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

### CI Stage 

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

 - !!! NOTE : AWS Credentails only available 12 hours. I have to re-generate a new one after 12 hours

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

**This way for single Pipeline in Github**

I want to trigger build automatically whenever commit is push to Repository 

Other ways to trigger build scheduling. For example I want this build to run every 2 hours 

Go to Github repo -> Settings -> Webhooks -> Click Add Webhooks -> Payload URL : `http://<my-jenkins-domain>/github-webhook/` -> Content type: application/json -> Choose just push event -> Save Webhook 

**This way for multiple Branches Pipeline**

I need a Plugin call Multibranch Scan Webhook Trigger 

Once installed I have `Scan by Webhook` in Multibranch Configuration -> Choose Scan by Webhook, then I have Trigger Token (This is a token that I can name whatever I want this Token will be use for the communication between Gitlab and Jenkin, or Github and Jenkins ...)

To use this token I will go to Github Repo -> Choose Webhook (Webhook is basically same Integration above) . 

The way it work is It will tell Github to send the notifications on specific URL using that token, and when Jenkins receive that a request it will trigger multibranch pipeline which has scan by webhook configured for that specific token . I don't need secret token 

#### Ignore Jenkins Commit for Jenkins Pipeline Trigger

I need someway to detect that commit was made by Jenkins not the Developer and Ignore the Trigger when the Commit from Jenkins 

I need the Plugin call `Ignore Commiter Strategy`

Go to my Pipeline Configuration -> Inside the Branch Sources I see the Build Strategy (This is an option just got through the plugin) -> In this option I will put the email address of the committer that I want to Ignore . I can provide a list of email

### CD Stage

I will setup automatic deployment in Kubernetes to the cluster in the pipeline 

I need to configure a couple of Steps in order for that to work : 

#### Install kubectl inside Jenkins Container

Connect to a Server : `ssh root@...`

Check running Container : `docker ps`

Go inside Jenkins container as a Root User bcs Jenkins doesn't admin permission : `docker exec -it -u 0 <container-id> bash`

Inside Jenkins container install kubectl, make it executable and move it to `/usr/local/bin/kubectl` : `curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl; chmod +x ./kubectl; mv ./kubectl /usr/local/bin/kubectl`

As I learned in Jenkins Moudule . Whenever I need some CLI tools execute commands with inside our Pipelines I can install them directly on a Jenkins Server or inside a Jenkins container and they will be available as Linux Command inside the Pipeline I can execute them pretty simply .

#### Install aws iam authenticator tool inside Jenkins Container

This is Specific to AWS . When I created EKS Cluster I got a Kubeconfig which contain for the Secret and all the certificate for authenticating and connecting, it also container the infomation to a Cluster on AWS specificly to EKS Cluster . So I will provide all the credentials, however I need kubectl and aws-iam-authenticator both to acctually connect to EKS cluster and authenticate to it from Jenkins

Install aws-iam-authentocator, make it executable and move it to /usr/local/bin :

```
curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.6.11/aws-iam-authenticator_0.6.11_linux_amd64

chmod +x ./aws-iam-authenticator

mv ./aws-iam-authenticator /usr/local/bin
```

#### Create Kubeconfig file to connect to EKS Cluster

I don't have the editor inside the Jenkins Container bcs it is a lightweight container . I will create the file outside on the Host and I just simple copy the file into Jenkin Container

Basic config file content 

```
apiVersion: v1
kind: Config
clusters:
- cluster:
   certificate-authority-data: <certificate-data>
   server: <endpoint-url>
 name: kubernetes
contexts:
- context:
   cluster: kubernetes
   user: aws
 name: aws
current-context: aws
users:
- name: aws
 user:
   exec:
     apiVersion: client.authentication.k8s.io/v1beta1
     command: /usr/local/bin/aws-iam-authenticator
     args:
       - "token"
       - "-i"
       - <cluster-name>

```

I need to put in Cluster name

I need to put in Server API Endpoint : I can get this from EKS overview in Management Console

The other thing I need to chagne is `certificate-authority-data` : This is something that get generated in EKS Cluster when its gets created and I have that file available on local in `kube/config`

When I have the file ready I go inside Jenkins and create kube folder : `docker exec -it <container-id> bash` then I will go back to Jenkins home cd `~`, to see the path use pwd I should see `/var/jenkins_home` then I create .kube folder mkdir `.kube` to store kubeconfig file

Then copy config file from host to Jenkins : `docker cp config container-id:/var/jenkins_home/.kube/`

#### Create AWS Credentials

Need Credentials for AWS Users . Locally I have work with the Admin User which is AWS User with its own secret key ID and access key . I need to configure the same for Jenkins

NOTE : I don't have to use Admin User to execute command on Jenkins . Best practice would be Create AWS IAM Jenkins User for different Services that Jenkins connect to and needs to authenticate to including Docker Repos, AWS, Kubernetes and so on. And I can give that User Limited Permission for security Reason

Inside Jenkins UI . Inside mutiple Branches Pipeline -> Go to Credentials -> Add Credentials -> Choose Secret Text

 - I will create 2 Credentials : `access_key_id` and `secret_key_id`

 - Locally my Credentials live here : `.aws/credentials`

Configure `access_key_id` and `secret_key_id` in Jenkinsfile will look like this:

```
stage("Deploy with Kubernetes") {
  environment{
    AWS_ACCESS_KEY_ID = credentials('Aws_Access_Key_Id')
    AWS_SECRET_ACCESS_KEY = credentials('Aws_Secret_Access_Key')
  }
  steps {
    script {

    }
  }
}
```

Now I can execute kubectl bcs I have it installed inside my Jenkins Container . With `kubectl` execution `aws-iam-authenticator` also execute in the background

Before kubectl execute success I need to set or export ENV that will be use in that connection . I am setting those 2 ENV as a Context for the kubectl command to execute bcs in the background IAM will be executed an that command will need Access Credentials to connect to AWS . Bcs I don't have .aws/config so I have to config like this .

**Wrap up**: Kubectl get executed which will use kubeconfig files created in .kube/config and inside that config file it is configure that aws-IAM-authenticator need to be use in order to authenticate with AWS account . And when `aws-iam-authenticator` command get trigger in the background, it need `AWS_ACCESS_KEY` and `AWS_SECRET_ACCESS_KEY` (AWS Credentials)

NOTE : This part of authentication where I need the aws-iam-authenticator and setting AWS-crenditals in addition to Kubernetes Authentication it acctually specific to AWS . Other platform will have different way to authenticate

Now I can try to test by Deploy Nginx like this :

```
stage("Deploy with Kubernetes") {
  environment{
    AWS_ACCESS_KEY_ID = credentials('Aws_Access_Key_Id')
    AWS_SECRET_ACCESS_KEY = credentials('Aws_Secret_Access_Key')
  }
  steps {
    script {
      echo "Deploy Nginx ...."
      sh 'kubectl create deployment nginx-deployment --image=nginx'
    }
  }
}
```

Now I could see my Nginx running by using `kubectl get pods`

<img width="600" alt="Screenshot 2025-04-28 at 13 35 41" src="https://github.com/user-attachments/assets/02d3c324-9320-405f-958e-cfd6826c11a8" />

Next steps I will deploy my Java Application ....

#### Add AWS credentials on Jenkins for AWS account authentication

In this Deployment Stage I will pull Image from the ECR . In order to pull Image from ECR I need to login to ECR .

 - I need to create a AWS ECR credentials Secret Component for Kubernetes in able to fetch Image from ECR

 - I need to get AWS ECR Password : `aws ecr get-login-password --region us-west-1`

 - I need a AWS ECR Server endpoint: `https://565393037799.dkr.ecr.us-west-1.amazonaws.com`

 - Username would be : `AWS`

The command to create Secret component in Kubernetes is : 

```
kubectl create secret docker-registry <my-secrect-name> \
--docker-server=https://565393037799.dkr.ecr.us-west-1.amazonaws.com \
--docker-username=AWS \
--docker-password=<AWS-ECR-Password> \
```

#### Configure Kubernetes Deployment and Service Yaml files

Couple things that need for a Pipeline to deploy Image on Kubernetes : 

 - I need Kubernetes Configuration for my applications's deployment and service . Everytime I want to deploy a new version of my Application I need to create Deployment and Service

 - I am generating a new Image everytime Pipeline run. The image is actually dynamic . I need to set Image dynamically in K8s Configfile

   - To set image in Deployment file : `$IMAGE_NAME`. This is a ENV that I set in a Jenkinsfile.
  
   - Also set `imagePullPolicy : always` always set a new Image when the Pod start no matter that specific Image available in that Local

   - Also set Apps name bcs it repeat multiple times in the file : $APP_NAME .

   - In Jenkinsfile . I set `$IMAGE_NAME` as a ENV in the Version Incrementation Stage . Now I will also set `$APP_NAME` as a ENV in the Deploy Stage (can be in Global) by using `environment{}` blocks.

My Yaml file would look like this : 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: $APP_NAME
  labels:
    app: $APP_NAME
spec: 
  replicas: 2 
  selector:
    matchLabels:
      app: $APP_NAME 
  template:
    metadata:
      labels: 
        app: $APP_NAME
    spec: 
      imagePullSecrets: 
      - name: docker-ecr-authentication
      containers:
      - name: $APP-NAME
        image: $IMAGE_NAME
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
        env: 
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: java-secret
              key: DB_USER
        - name: DB_NAME 
          valueFrom: 
            secretKeyRef:
              name: java-secret
              key: DB_NAME
        - name: DB_SERVER
          valueFrom:
            configMapKeyRef:
              name: java-configmap
              key: database_server
        - name: DB_PWD
          valueFrom: 
            secretKeyRef:
              name: mysql
              key: mysql-password

---

apiVersion: v1
kind: Service
metadata: 
  name: java-app-service
spec:
  selector:
    app: $APP_NAME
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
```

#### Create Deployment Stage in Jenkinsfile

To pass value from Jenkinsfile to Yaml file: 

 - I use the command line tool call Environment Subsitute `envsubst`. This command actually used to substitute any variables define inside the file (in this case Yaml file) . And the syntax this command expect is `IMAGE_NAME`

 - This tool I need to install inside Jenkins Container:

   - SSH to Jenkins server : ssh root@
  
   - Get into Jenkins container : docker exec -it -u 0 <container-id> bash
  
   - Install gettext-base : apt-get install gettext-base
  
 - I pass a file to `envsubst` command `envsubst < config.yaml` . It will take that file and it will look for a syntax of `$` and name of Variable and it will try to match that name of the variable to any ENV defined in that context . Then it will create a temporary file with the values set and I will pipe that temporary file and pass it as a parameter like this : `envsubst < config.yaml | kubectl apply -f`

My Deployment Stage will look like this :

```
stage("Deploy with Kubernetes") {
  environment{
    AWS_ACCESS_KEY_ID = credentials('Aws_Access_Key_Id')
    AWS_SECRET_ACCESS_KEY = credentials('Aws_Secret_Access_Key')
    APP_NAME = "java-app"
  }
  steps {
    script {
      echo "Deploy Java Application ...."
      sh "envsubst < Kubernetes/java-app.yaml | kubectl apply -f -"
    }
  }
}
```

#### Create Deployment Stage and Deploy with HelmChart in Jenkinsfile 

First I need to install helm inside my Jenkin container 

 - SSH to a server where a Jenkins container is running : `ssh -i ~/.ssh/id_rsa root@<ip-address>`

 - Go inside Jenkins container : `docker exec -it -u 0 <container-id> bash`

 - To check the Linux Distribution of my Container : `cat /etc/os-release`

 - To install Helm Chart on Debian GNU/Linux

  ```
  # 1. Install required dependencies
  apt update && apt install -y curl gnupg
  
  # 2. Add Helm GPG key
  curl https://baltocdn.com/helm/signing.asc | gpg --dearmor -o /usr/share/keyrings/helm.gpg
  
  # 3. Add Helm apt repository
  echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" \
    | tee /etc/apt/sources.list.d/helm-stable-debian.list
  
  # 4. Update apt and install helm
  apt update && apt install -y helm
  
  # 5. Verify installation
  helm version
  ```

Now I will configure my acutal `values.yaml` files 

```
# Deployment and Service
AppName: $APP_NAME
ServiceName: java-app-service
ReplicasCount: 2
ImagePullSecretName: docker-authenticate-ecr
ImageName: $IMAGE_NAME
ContainerPort: 8080
ServicePort: 8080

InitialDelaySeconds: 50 # NOTE !!! : Java App may take 30s to start . I will delay my rediness prope and liveness prope to 50s 
PeriodSeconds: 5

RequestCPU: 100m
RequestMemory: 64Mi
LimitCPU: 100m
LimitMemory: 200Mi

regularENV: {}

secretName: java-secret
secretData: 
  DB_USER: dGlt
  DB_NAME: bXktYXBwLWRi

configMapName: java-config 
configData: 
  database_server: "mysql-primary-0.mysql-primary-headless"

ingressName: java-ingress
ingressHost: hostURL
```

Now I configure in my Deploy Stage 

```
stage("Deploy with Kubernetes") {
  environment{
    AWS_ACCESS_KEY_ID = credentials('Aws_Access_Key_Id')
    AWS_SECRET_ACCESS_KEY = credentials('Aws_Secret_Access_Key')
    APP_NAME = "java-app"
  }
  steps {
    script {
      echo "Deploy Java Application "
      sh "envsubst < charts/values/java-gradle-values.yaml | helm upgrade --install ${APP_NAME} ./charts/java-gradle-deployment -f -"
    }
}
```

`helm upgrade --install ${APP_NAME} ./charts/java-gradle-deployment -f -`: This command mean if the release ${APP_NAME} doesn't exist yet . It will be installed . If the release already exists → it will be updated/upgraded with new values or image versions.


## Configure Autoscaling for EKS Cluster

Thing I need in order to create Auto Scaler 

 - I need Auto Scaling Group (Can change Min and Max anytime)

 - I need to Create Auto Scaler Policy

 - Then create Auto Scaler Role

 - Then I use OpenID Provider OIDC federated authentication allow my service to assume an IAM role and interact with AWS Service without have to store AWS_ACCESS_KEY and AWS_ACCESS_SECRET_KEY

 - This I will attach that to Service Account

This is my Auto Scaler Yaml files include : Service Account, Role, ClusterRoleBinding, RoleBinding and Deployment (https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml) 

#### Create custom Autoscaler Policy for my NodeGroup

In IAM -> go to Policy -> go to Create Policy -> Choose JSON then paste the custom list of Policy in there -> Then give it a name and create it

Custome list is the one that has a list of all the Permissions that we need to give NodeGroup IAM role for the autoscaling to work

After Policy created . Create OIDC for authentication

```
{
 "Version": "2012-10-17",
 "Statement": [
     {
         "Effect": "Allow",
         "Action": [
             "autoscaling:DescribeAutoScalingGroups",
             "autoscaling:DescribeAutoScalingInstances",
             "autoscaling:DescribeLaunchConfigurations",
             "autoscaling:DescribeScalingActivities",
             "ec2:DescribeImages",
             "ec2:DescribeInstanceTypes",
             "ec2:DescribeLaunchTemplateVersions",
             "ec2:GetInstanceTypesFromInstanceRequirements",
             "eks:DescribeNodegroup"
         ],
         "Resource": [
             "*"
         ]
     },
     {
         "Effect": "Allow",
         "Action": [
             "autoscaling:SetDesiredCapacity",
             "autoscaling:TerminateInstanceInAutoScalingGroup"
         ],
         "Resource": [
             "*"
         ]
     }
 ]
}
```

#### Create OIDC Provider

**Prerequisites:**

An Active EKS cluster (1.14 preferred since it is the latest) against which the user is able to run kubectl commands.

Cluster must consist of at least one worker node ASG.

**Create an IAM OIDC identity provider for your cluster with the AWS Management Console**

Open the Amazon EKS console.

In the left pane, select Clusters, and then select the name of your cluster on the Clusters page.

In the Details section on the Overview tab, note the value of the OpenID Connect provider URL.

Open the IAM console at `https://console.aws.amazon.com/iam/`.

In the left navigation pane, choose Identity Providers under Access management. If a Provider is listed that matches the URL for your cluster, then you already have a provider for your cluster. If a provider isn’t listed that matches the URL for your cluster, then you must create one.

To create a provider, choose Add provider.

For Provider type, select OpenID Connect.

For Provider URL, enter the OIDC provider URL for your cluster.

For Audience, enter sts.amazonaws.com.

(Optional) Add any tags, for example a tag to identify which cluster is for this provider.

Choose Add provider.

## Create an IAM role for your service accounts in the console

Retrieve the OIDC issuer URL from the Amazon EKS console description of your cluster . It will look something identical to: 'https://oidc.eks.us-east-1.amazonaws.com/id/xxxxxxxxxx'

While creating a new IAM role, In the "Select type of trusted entity" section, choose "Web identity".

In the "Choose a web identity provider" section: For Identity provider, choose the URL for your cluster. For Audience, type sts.amazonaws.com.

In the "Attach Policy" section, select the policy to use for your service account, that you created in Section B above.

After the role is created, choose the role in the console to open it for editing.

Choose the "Trust relationships" tab, and then choose "Edit trust relationship". Edit the OIDC provider suffix and change it from :aud to :sub. Replace sts.amazonaws.com to your service account ID.

 - Service Account ID is : `system:serviceaccount:<namespace>:<service-account-name>`.

Update trust policy to finish.

#### Configure Tags on Autoscaling Group Automate created by AWS

Tags are also use in order for different Services or Component to Read and Detect some Information from each other . This is one of the case where we have Tags that auto scaler that we will deploy inside Kubernetes will require to auto Discover Autoscaling group in the AWS account . So the Cluster Autoscaler Component, which I gonna deploy inside Kubernetes Cluster, needs to communicate with auto scaling group . For this communication to happen the Cluster auto Scaler first needs to detect the auto scaling group from AWS . And it happen by using these 2 tags : k8s.io/cluster-autoscaler/eks-cluster-test, k8s.io/cluster-autoscaler/enable

#### Deploy Cluster Autoscaler

In that Yamlfile :

 - In the command part I have the Configuration where NodeGroup auto discover is configured using these tags : --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/

 - In the Service Account section add the code below . This is how I attach Auto-scaler-role to Service Account .

  ```
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::565393037799:role/Auto-Scaler-Role
  ```

 - In the Deployment section add Region ENV :

  ```
  env:
  - name : AWS_REGION
    value : us-west-1
  ```

To check my Deployment : `kubectl get deployment -n kube-system cluster-autoscaler`

## Create a Shared Library 

I will create another branch to test Jenkins Shared Lib : `git checkout -b Use_Shared_Lib`

Let's say I have a Java Gradle Project that have multiple Microservices . Each Mirco have 1 Pipeline . I would build each Microservice as a Separate Application . Most of the build Logic are same like docker build, docker login etc ... logic . But I don't want to repeat myself with the same logic it is so unefficient

The efficient way to do is Jenkin Shared library . This is an extension to Pipeline and Jenkins Shared Library is its own Repository which is written in Groovy, and I can write all the Logic that is gonna be shared accross many different applications in the shared Library and reference that Logic from Jenkinfile in each project . This is for 1 project that have multiple Microservices

#### Structure of Jenkins Share Lib


```
my-shared-library/
├── vars/
│   └── myFunction.groovy       # Global function (callable in Jenkinsfile as `myFunction()`)
├── src/
│   └── com/
│       └── example/
│           └── MyClass.groovy  # Helper classes
├── README.md
```

**vars folder**: All the files that I have here all those executable functions, I am referencing them by the name of the file . I will use that to call that function in Jenkinfiles

For example : This is a buildJar.groovy function: 

```
#!/user/bin/env groovy

# This line above is let my editor detect that I am working with Groovy script . I can use the same annotation in Jenkinfiles to let my editor know that I am working with Groovy script bcs the function has no .groovy

def call () {
  echo 'I am building a Jar'
  sh 'gradle build'
}
```

For example : this is a buildImage.groovy function . 

```
#!/user/bin/env groovy

# This line above is let my editor detect that I am working with Groovy script . I can use the same annotation in Jenkinfiles to let my editor know that I am working with Groovy script bcs the function has no .groovy

def call (String ECR_REPO, String IMAGE_NAME, String ECR_SERVER_URL) {
   echo "building the docker image..."
   withCredentials([usernamePassword(credentialsId: 'AWS_Credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]){
      sh "docker build -t ${ECR_REPO}:${IMAGE_NAME} ."
      sh "echo $PASS | docker login -u $USER --password-stdin ${ECR_SERVER_URL}"
      sh "docker push ${ECR_REPO}:${IMAGE_NAME}"
}
```

and I can use it in Jenkinsfile like this : 

```
@Library('your-shared-library-name') _

pipeline {
    agent any

    environment {
        IMAGE_NAME = "v1.0.0"
        ECR_REPO = "123456789012.dkr.ecr.us-west-1.amazonaws.com/my-app"
        ECR_URL_SERVER = "123456789012.dkr.ecr.us-west-1.amazonaws.com"
    }

    stages {
        stage('Build and Push') {
            steps {
                dockerPush(env.IMAGE_NAME, env.DOCKER_REPO, env.DOCKER_REPO_SERVER)
            }
        }
    }
}
```

#### Make Share Library Available

Go to Manage Jenkin -> System -> Global Pipeline Library -> Add Library -> Provide Git URL (Mordern SCM) , Add credentials

**Default version** : This can be either branch, this can also be commit hash or this can be a tag . Just like any other Application I should version my Jenkin Share Lib so whenever I make change I create a new version, tag and then I can reference that tag here . If I define Main Branch here then every new change or every commit to a Repo will be immediately available in whole Jenkin for all the Pipeline and it is a breaking change then my Jenkin file or my Pipeline will not work anymore so having a fixed version is a good idea

With `Allow default version to be overridden` I can override that default version in my Jenkinfile If I want to


#### Use Share Library in Jenkinfile

In order to use function in Jenkins Share Lib I have to import that Share Lib : `@Library('jenkins-shared-lib')` . I can also choose specific version if I have `@Library('jenkins-shared-lib@2.0')`

 - `jenkins-shared-lib`: Is the name that I gave when defining in global library

 - !!! NOTE : If I don't have import statement or variable definition directly after the Import I have to add `_` like this `@Library('jenkins-shared-lib')_`

#### Project Scoped Share Library

Let say I have Jenkins share Lib but for only my project, other project or other team may not need them maybe I want to share them for my own pipeline . So I don't need to add Share Lib in Global Library and instead I will refererence library directly from Jenkinsfile

```
library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
    [$class: 'GitSCMSource',
     remote: https://github.com/ManhTrinhNguyen/Jenkins-Docker-Excercise-Shared-Library.git,
     credentialsId: 'github-credentials'
    ]
)
```

#### Increment Gradle Version 

I will extract Increment Gradle Version to Shared Lib 

This is for patch version only .

In `vars/Increment_Gradle_Version.groovy` : 

```
def call (String Update_Version) {
  echo "Increment App Version"

  sh "gradle ${Update_Version}"  

  def version = readProperties(file: 'version.properties')

  env.IMAGE_NAME = "${ECR_REPO}:${version['major']}.${version['minor']}.${version['patch']}"

  echo "${env.IMAGE_NAME}"
}
```

Bcs ${ECR_REPO} is in my Global Environment Variable so I don't need to set it as a Parameter 

The way I use `Increment_Version_Gradle` function is I called that function and pass a parameter to it 

 - To Increase Major Version pass ` majorVersionUpdate`

 - To Increase Minor Version pass `minorVersionUpdate`

 - To Increase Patch Version pass `patchVersionUpdate`

```
stage("Version Increment Dynamic"){
    steps {
      script {
        Increment_Version_Gradle("patchVersionUpdate")
      }
    }
}
```

#### Build Gradle Jar

I will create a new file `vars/Build_Gradle_Jar.groovy`. Then I will extract a build Jar logic into that function 

```
def call(){
  echo "Build Gradle Jar ...."

  sh 'gradle clean build'
}
```

Now I have a Build Gradle Jar function I can use it in my Jenkinfile like this : 

```
stage("Build Jar") {
  steps {
    script {
      Build_Gradle_Jar()
    }
  }
}
```

#### Build Docker Image 

I will create a new file `vars/Docker_Build_Image.groovy`. Then I will extract a Docker Image logic into that function 

```
#!/user/bin/env groovy

def call () {
  echo "Build Docker Image ...."

  sh "docker build -t ${env.IMAGE_NAME} ."
}
```

Then I can use Docker_Build Image like this : 

```
stage("Build Docker Image") {
  steps {
    script {
      Docker_Build_Image()
    }
  }
}
```

#### Docker Login to ECR 

I will create a new file `vars/Docker_Login_ECR.groovy`. Then I will extract a Docker Image Logic into that function 

```
#!/user/bin/env groovy

def call() {
    echo "Login to Docker Hub"

    withCredentials([
            usernamePassword(credentialsId: 'Docker_Hub_Credential', usernameVariable: 'USER', passwordVariable: 'PWD')
    ]){
        sh "echo ${PWD} | docker login -u ${USER} --password-stdin"
    }
}
```

I don't need to pass `${ECR_URL}` as a Parameter bcs it is already in my Global ENV 

Then I can use `Docker_Login_ECR` function in my Jenkinsfile like this: 

```
stage("Login to ECR") {
  steps {
    script {
      Docker_Login_ECR()
    }
  }
}
```

#### Push Docker Image To ECR

I will create a new file `vars/Docker_Push_Image.groovy` . Then I will extract a Docker Push Image Logic into that function 

```
#!/user/bin/env groovy

def call () {
    sh "docker push ${IMAGE_NAME}"
    echo "Push Image Success ....."
}
```

I have `${IMAGE_NAME}` as a ENV so I don't need to pass it as a Parameters . So my Jenkinsfile will look like this

```
stage("Push Docker Image to ECR") { 
  steps {
    script {
      Docker_Push_Image()
    }
  }
}
```

#### Commit to Git Repo 

I will create a new file `vars/Commit_to_Git_Repo.groovy` . Then extract all the logic to that 

```
def call (String BRANCH_NAME){
    withCredentials([
        usernamePassword(credentialsId: 'Github_Credential', usernameVariable: 'USER', passwordVariable: 'PWD')
    ]){
    // To set configuration that kept in .git folder and global configuration in git .
    // I want to set git config Global I can put a flag --global
    sh 'git config --global user.email "jenkin@gmail.com"' // If there is no User Email at all, Jenkin will complain when commiting changes . It will say there is no email that was detected to attach to as a metadata to that commit
    sh 'git config --global user.name "Jenkins"'

    // Set Origin access
    sh "git remote set-url origin https://${USER}:${PWD}@${GIT_URL}"

    sh "git add ."
    sh 'git commit -m "ci: version bump"'
    sh "git push origin HEAD:${BRANCH_NAME}"
    }
}
```

I have `${GIT_URL}` as a ENV . So I don't need to pass it as a parameter . 

I also have `${BRANCH_NAME}` as a ENV provided by Jenkin, So I don't need to pass it as a parameter as well. 

My Jenkinfile will look like this 

```
stage("Commit to Git") {
  steps {
    script {
      Commit_to_Git_Repo()
    }
  }
}
```

#### Deploy with Kubernetes

I will create a new file `vars/Deploy_with_Kubernetes.groovy`. Then I will extract the logic into that function 

I will pass a Parameter to it as a path to my Kubernetes Yaml file 

```
def call(String K8_Deploy_Yaml_file) {
  echo "Deploy Java Application "
  sh "envsubst < ${K8_Deploy_Yaml_file} | kubectl apply -f -"
}
```

Then I Can use it in my Jenkinfile like this : 

```
stage("Deploy with Kubernetes") {
  environment{
    AWS_ACCESS_KEY_ID = credentials('Aws_Access_Key_Id')
    AWS_SECRET_ACCESS_KEY = credentials('Aws_Secret_Access_Key')
    APP_NAME = "java-app"
  }
  steps {
    script {
      Deploy_with_Kubernetes("Kubernetes/java-app-configmap.yaml")
      Deploy_with_Kubernetes("Kubernetes/java-app-ingress.yaml")
      Deploy_with_Kubernetes("Kubernetes/java-app-secret.yaml")
      Deploy_with_Kubernetes("Kubernetes/java-app.yaml")
    }
  }
}
```














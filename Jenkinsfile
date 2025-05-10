library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
    [$class: 'GitSCMSource',
     remote: 'https://github.com/ManhTrinhNguyen/Share_Library_Exercise.git',
     credentialsId: 'Github_Credential'
    ]
)

pipeline {   
    agent any
    tools {
        gradle 'gradle-8.14'
    }

    environment {
      ECR_URL = "565393037799.dkr.ecr.us-west-1.amazonaws.com"
      ECR_REPO = "${ECR_URL}/java-app"
      GIT_URL = "github.com/ManhTrinhNguyen/AWS-EKS-exercise.git"
    }

    stages {
      
        stage("Version Increment Dynamic"){
            steps {
              script {
                Increment_Version_Gradle("patchVersionUpdate")
              }
            }
        }

        stage("Build Jar") {
          steps {
            script {
              Build_Gradle_Jar()
            }
          }
        }

        stage("Build Docker Image") {
          steps {
            script {
              Docker_Build_Image()
            }
          }
        }

        stage("Login to ECR") {
          steps {
            script {
              Docker_Login_ECR()
            }
          }
        }

        stage("Push Docker Image to ECR") { 
          steps {
            script {
              Docker_Push_Image()
            }
          }
        }

        stage("Deploy with Kubernetes") {
          environment{
            AWS_ACCESS_KEY_ID = credentials('Aws_Access_Key_Id')
            AWS_SECRET_ACCESS_KEY = credentials('Aws_Secret_Access_Key')
            APP_NAME = "java-app"
          }
          steps {
            script {
              Deploy_with_Kubernetes("Kubernetes/java-app.yaml")
            }
          }
        }

        stage("Commit to Git") {
          steps {
            script {
              Commit_to_Git_Repo()
            }
          }
        }
    }
} 

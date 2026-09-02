// library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
//     [$class: 'GitSCMSource',
//      remote: 'https://github.com/ManhTrinhNguyen/Share_Library_Exercise.git',
//      credentialsId: 'Github_Credential'
//     ]
// )

pipeline {   
    agent any
    tools {
        gradle 'gradle-8.0'
    }

    environment {
      ECR_URL = "660753258283.dkr.ecr.us-west-1.amazonaws.com"
      ECR_REPO = "${ECR_URL}/java-app"
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

        stage("Build Jar") {
          steps {
            script {
              echo "Build Gradle Jar ...."

              sh 'gradle clean build'
            }
          }
        }

        stage("Build Docker Image") {
          steps {
            script {
              echo "Build Docker Image ...."

              sh "docker build -t ${env.IMAGE_NAME} ."
            }
          }
        }

        stage("Login to ECR") {
          steps {
            script {
              withCredentials([
                usernamePassword(credentialsId: 'ecr_credential', usernameVariable: 'USER', passwordVariable: 'PWD')
              ]){
                sh "echo ${PWD} | docker login --username ${USER} --password-stdin ${ECR_URL}"

                echo "Login successfully"
              }
            }
          }
        }

        stage("Push Docker Image to ECR") {
          steps {
            script {
              sh "docker push ${IMAGE_NAME}"
              echo "Push Image Success ....."
            }
          }
        }

        // Clone or Pull K8s repo to Jenkins 

        stage("Clone/Pull K8s Repo") {
          steps {
            script {
              if(fileExists('GitOps')) {
                echo 'Cloned repo already exist - Pulling latest changes'

                dir("GitOps") {
                  sh 'git pull'
                }
              } else {
                echo 'Repo does not exist - Cloning Repo' 
                sh 'git clone https://github.com/ManhTrinhNguyen/GitOps.git GitOps'
              }
            }
          }
        }

        // Update K8s manifests

        stage("Update K8s manifests") {
          steps {
            dir("GitOps/java-app") {
              sh """
                sed -i "s|image: .*|image: ${IMAGE_NAME}|g" java-deployment.yaml
              """

              sh 'cat java-deployment.yaml'
            }
          }
        }

        // Commit to git 

        stage("Commit to GitHub") {
          steps {
            script {
              withCredentials([
                usernamePassword(credentialsId: 'github_credential', usernameVariable: 'USER', passwordVariable: 'PWD')
              ]){
                // To set configuration that kept in .git folder and global configuration in git .
                // I want to set git config Global I can put a flag --global
                sh 'git config --global user.email "jenkin@gmail.com"' // If there is no User Email at all, Jenkin will complain when commiting changes . It will say there is no email that was detected to attach to as a metadata to that commit
                sh 'git config --global user.name "Jenkins"'

                // Set Origin access
                sh "git remote set-url origin https://${USER}:${PWD}@github.com/ManhTrinhNguyen/GitOps.git"

                sh "git add ."
                sh 'git commit -m "ci: version bump"'
                sh 'git push origin HEAD:main'
              }
            }
          } 
        }
    }
} 

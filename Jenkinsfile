// library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
//     [$class: 'GitSCMSource',
//      remote: 'https://github.com/ManhTrinhNguyen/Share_Library_Exercise.git',
//      credentialsId: 'Github_Credential'
//     ]
// )

pipeline {   
    agent any
    tools {
        gradle 'gradle-8.14'
    }

    stages {
        stage("Version Increment Dynamic"){
            steps {
                script {
                    echo 'Increase Patch Version ....'

                    sh 'gradle patchVersionUpdate'

                    // Read file content
                    def props = new Properties()
                    def fileContent = readFile('version.properties')
                    props.load(new StringReader(fileContent))

                    echo "Major Version: ${props['major']}"
                    echo "Minor Version: ${props['minor']}"
                    echo "Patch Version: ${props['patch']}"
                }
            }
        }
    }
} 

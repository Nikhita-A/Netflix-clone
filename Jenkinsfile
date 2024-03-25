pipeline {
    agent any
    options {
        timeout(time: 20, unit: 'MINUTES') 
    }
    triggers { 
        pollSCM('* * * * *') 
    }
    tools {
        jdk 'JDK 17'
        nodejs 'NodeJS 21'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('Clean Workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Git Checkout'){
            steps{
                git url: 'https://github.com/Nikhita-A/Netflix-clone.git',
                    branch: 'main'
            }
        }
        stage('SonarQube Analysis'){
            steps{
                withSonarQubeEnv('sonar-server')
                sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix-clone \
                -Dsonar.projectKey=Netflix-clone '''
            }
        }
        stage('Quality Gate'){
            steps{
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar_token'
            }
        }
        stage('Docker Image Build'){
            steps{
                sh "docker image build -t netflix:$BUILD_ID ."
            }
        }
        stage('Trivy Scan'){
            steps{
                sh "trivy image netflix:$BUILD_ID > trivyreport.txt"
            }
        }
        stage('Docker Image Push'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                        sh "docker tag netflix:$BUILD_ID nikhitaa/netflix-clone:$BUILD_ID"
                        sh "docker push nikhitaa/netflix-clone:$BUILD_ID"
                    }
                }
            }
        }
    }
}
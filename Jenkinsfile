pipeline{
    agent any
    tools{
        jdk 'JAVA 17'
        nodejs 'NodeJS 21'
    }
    environment {
        SCANNER_HOME=tool 'SONAR_SCANNER'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/Nikhita-A/Netflix-clone.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('SONAR_SERVER') {
                    sh ''' $SCANNER_HOME/bin/SONAR_SCANNER -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar_Token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}
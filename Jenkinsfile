pipeline {
    agent any
    
    tools {
        nodejs 'node21'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/gouthamc125/3-Tier-Full-Stack.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Unit Test cases') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=camp -Dsonar.projectName=camp'''
                }
            }
        }
        
        stage('Filesystem scan TRIVY') {
            steps {
                sh 'trivy fs --format table -o trivy_fs.html .'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                    sh 'docker build -t gouthamc125/camp:latest .'
                   }
                }
            }
        }
        
        stage('Scan the Image') {
            steps{
                sh 'trivy image --format table -o trivy_image.html gouthamc125/camp:latest'
            }
        }
        
        stage('Deploy Container') {
            steps {
                sh 'docker run -it -d --name camp -p 80:3000 gouthamc125/camp:latest'
            }
        }
    }
}

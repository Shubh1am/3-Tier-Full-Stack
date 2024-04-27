pipeline {
    agent any
    
    tools{
        nodejs "nodejs"
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/Shubh1am/3-Tier-Full-Stack.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('Unit Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs --format table -o fs-report.html .'
            }
        }
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar') {
                    
                sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Campground -Dsonar.projectKey=Campground'
                }
            }
        }
        stage('Docker build and tag') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred ', toolName: 'docker')  {
                        sh 'docker build -t shubhammutkalwar/camp:latest .'
                    }
                }     
            }
        }    
        stage('Trivy image Scan') {
            steps {
                sh 'trivy fs --format table -o fs-report.html shubhammutkalwar/camp:latest'
            }
        }
        stage('Docker Push Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker')  {
                        sh 'docker push shubhammutkalwar/camp:latest'
                    }
                }     
            }
        }
        stage('Deploy to Dev') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', ) {
                        sh 'docker run -d -p 3000:3000 shubhammutkalwar/camp:latest'
                    }
                }     
            }
        }   
    }
}

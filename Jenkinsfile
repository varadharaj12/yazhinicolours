
pipeline {
    agent any

    environment {
        // Environment variables
    }

    tools {
        jdk 'java17'
         maven 'maven3'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/varadharaj12/yazhinicolours'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh 'mvn sonar:sonar'
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Trivy File Scan') {
            steps {
                sh 'trivy fs .'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t app .'
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image app'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push image'
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

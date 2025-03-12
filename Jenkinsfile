pipeline {
    agent any

    tools {
        maven 'Maven 3'
    }

    environment {
        SONARQUBE_URL = 'http://172.17.0.3:9000'  // Update with the container IP
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Sathvik-ai/spring-petclinic.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { 
                    withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONARQUBE_TOKEN')]) {
                        sh 'mvn sonar:sonar -Dsonar.projectKey=spring-petclinic -Dsonar.host.url=$SONARQUBE_URL -Dsonar.login=$SONARQUBE_TOKEN'
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh '''
                docker build -t sathvik-ai/spring-petclinic .
                docker tag sathvik-ai/spring-petclinic:latest sathvik-ai/spring-petclinic:v1
                docker push sathvik-ai/spring-petclinic:v1
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 8080:8080 --name petclinic sathvik-ai/spring-petclinic:v1'
            }
        }
    }
}

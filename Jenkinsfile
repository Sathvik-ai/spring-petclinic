pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://your-sonarqube-server:9000'  // Update with your SonarQube server URL
        DOCKER_IMAGE = 'sathvik-ai/spring-petclinic'  // Change as needed
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Sathvik-ai/spring-petclinic.git'
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
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE .
                docker tag $DOCKER_IMAGE:latest $DOCKER_IMAGE:v1
                docker push $DOCKER_IMAGE:v1
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 8080:8080 --name petclinic $DOCKER_IMAGE:v1'
            }
        }
    }
}

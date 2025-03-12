pipeline {
    agent any

    tools {
        maven 'Maven 3'  // Ensure this matches the name in Jenkins tool configuration
    }

    environment {
        SONARQUBE_URL = 'http://localhost:9000'  // Updated to use localhost
        DOCKER_IMAGE = 'sathvik-ai/spring-petclinic'  // Change as needed
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
                withSonarQubeEnv('SonarQubeServer') {  // Ensure it matches the SonarQube name in Jenkins
                    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_AUTH')]) {
                        sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=spring-petclinic \
                        -Dsonar.host.url=$SONARQUBE_URL \
                        -Dsonar.login=$SONAR_AUTH \
                        -Dsonar.maven.plugin.version=4.0.0.4121
                        '''
                    }
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

pipeline {
    agent any

    tools {
        maven 'Maven 3'  // Ensure this matches the name in Jenkins tool configuration
    }

    environment {
        SONARQUBE_URL = 'http://localhost:9000'  // Update as per your SonarQube instance
        SONAR_PROJECT_KEY = 'spring-petclinic'   // Unique SonarQube project key
        DOCKER_IMAGE = 'sathvik-ai/spring-petclinic'  // Your Docker image name
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
                withSonarQubeEnv('SonarQubeServer') {  // Matches your SonarQube server config
                    sh '''
                    mvn sonar:sonar \
                        -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                        -Dsonar.host.url=$SONARQUBE_URL \
                        -Dsonar.login=$SONAR_TOKEN  # Ensure SONAR_TOKEN is configured in Jenkins credentials
                    '''
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE:latest .
                docker tag $DOCKER_IMAGE:latest $DOCKER_IMAGE:v1
                docker push $DOCKER_IMAGE:v1
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop petclinic || true
                docker rm petclinic || true
                docker run -d -p 8080:8080 --name petclinic $DOCKER_IMAGE:v1
                '''
            }
        }
    }
}

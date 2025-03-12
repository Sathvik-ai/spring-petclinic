pipeline {
    agent any

    tools {
        maven 'Maven 3'  // Ensure this matches the name in Jenkins tool configuration
    }

    environment {
        SONARQUBE_URL = 'http://localhost:9000'  
        DOCKER_IMAGE = 'sathvik-ai/spring-petclinic'  
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
                withSonarQubeEnv('SonarQubeServer') {  
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

        stage('Quality Gate Check') {
            steps {
                script {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline failed due to quality gate failure: ${qg.status}"
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

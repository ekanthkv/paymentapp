pipeline {
    agent any

    environment {
        COMPOSE_FILE = 'micro-services/docker-compose.yml'
	DOCKER_USERNAME = 'ekanthkv' // Replace with your Docker username
        IMAGE_NAME = 'paymentapp'
        VERSION = '1.0.0'
        NEXUS_REPO_URL = 'http://localhost:8082/repository/paymentapp/' // Nexus Repository URL
        NEXUS_CREDENTIALS_ID = 'nexus-credentials' // Jenkins Credentials ID for Nexus
    }

    stages {
        stage('Checkout SCM') {
            steps {
                // Pull the repository from SCM
                checkout scm
            }
        }
        
        stage('Build and Deploy') {
            steps {
                script {
                   
                        // Validate docker-compose file
                        sh 'docker-compose config'

                        // Build and deploy services
                        sh 'docker-compose up --build -d'
                    
                }
            }
        }

	stage('Push to Nexus') {
    steps {
        script {
            docker.withRegistry("http://localhost:8082/repository/paymentapp", 'nexus-credentials') {
                // List images from docker-compose
                def images = sh(script: "docker-compose config | grep image: | awk '{print $2}'", returnStdout: true).trim().split('\n')
                
                // Tag and push each image
                for (image in images) {
                    def imageName = image.split(':')[0]
                    def tag = image.split(':')[1] ?: 'latest'

                    // Tagging the image for Nexus
                    sh "docker tag ${image} localhost:8082/repository/paymentapp/${imageName}:${tag}"

                    // Pushing the image to Nexus
                    sh "docker push localhost:8082/repository/paymentapp/${imageName}:${tag}"
                }
            }
        }
    }
}




        stage('Post-Deployment Verification') {
            steps {
                script {
                    // Ensure all containers are running
                    sh 'docker ps'

                    // Example: check service health or logs
                    sh 'docker-compose logs --tail=100'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Please check the logs.'
            // Cleanup if needed
            script {
                sh 'docker-compose down'
            }
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}


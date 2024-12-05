pipeline {
    agent any

    environment {
        COMPOSE_FILE = 'micro-services/docker-compose.yml'
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
                    // Navigate to the directory containing docker-compose.yml
                    dir('micro-services') {
                        // Validate docker-compose file
                        sh 'docker-compose config'

                        // Build and deploy services
                        sh 'docker-compose up --build -d'
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


pipeline {
    agent any
    
    tools {
        docker 'docker'
    }
    
    environment {
        DOCKER_IMAGE = 'my-app:latest'
        CONTAINER_NAME = 'my-app-container'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/your-username/your-repo.git',
                    credentialsId: 'github-credentials'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }
        
        stage('Stop and Remove Old Container') {
            steps {
                script {
                    try {
                        sh "docker stop ${CONTAINER_NAME} || true"
                        sh "docker rm ${CONTAINER_NAME} || true"
                    } catch (Exception e) {
                        echo "No existing container to stop"
                    }
                }
            }
        }
        
        stage('Run New Container') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE}").run(
                        "--name ${CONTAINER_NAME} -p 3000:3000 -d"
                    )
                }
            }
        }
        
        stage('Cleanup') {
            steps {
                script {
                    // Удаляем старые образы
                    sh 'docker image prune -f'
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed'
        }
        success {
            echo 'Application deployed successfully!'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}

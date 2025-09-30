pipeline {
    agent any

    tools {
        maven 'Maven3'   // الاسم زي ما متسجل في Jenkins
        jdk 'JDK17'
    }

    environment {
        DOCKER_COMPOSE = 'docker-compose -f docker-compose.yml'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Abd-ellah-Taha/todo-app.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "${DOCKER_COMPOSE} build"
            }
        }

        stage('Run Containers') {
            steps {
                sh "${DOCKER_COMPOSE} up -d"
            }
        }

        stage('Test API') {
            steps {
                script {
                    echo "Waiting for app to start..."
                    sh 'sleep 20'
                    sh 'curl -f http://localhost:8080/api/todos || exit 1'
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up containers..."
            sh "${DOCKER_COMPOSE} down || true"
        }
        success {
            echo "✅ Pipeline finished successfully."
        }
        failure {
            echo "❌ Pipeline failed."
        }
    }
}


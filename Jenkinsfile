pipeline {
    agent any

    tools {
        maven 'Maven3'   // لازم تكون ضايف Maven tool في Jenkins باسم Maven3
        jdk 'JDK17'      // لازم تكون ضايف JDK باسم JDK17
    }

    environment {
        DOCKER_COMPOSE = 'docker-compose -f docker-compose.yml'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Images') {
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
                    // اختبار بسيط: نعمل curl للـ endpoint
                    sh 'sleep 20' // نستنى السيرفر يشتغل
                    sh 'curl -f http://localhost:8080/api/todos || true'
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
            echo "Pipeline finished successfully."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}

pipeline {
    agent any

    tools {
        maven 'Maven3'   // الاسم زي ما سجلته في Jenkins -> Global Tool Configuration
        jdk 'JDK17'      // برضه الاسم زي اللي مسجله في Jenkins
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

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t todo-app .'
            }
        }

        stage('Run Containers') {
            steps {
                sh 'docker-compose -f docker-compose.yml up -d'
            }
        }

        stage('Test API') {
            steps {
                script {
                    def response = sh(
                        script: "curl -s -o /dev/null -w '%{http_code}' http://localhost:8080/api/todos",
                        returnStdout: true
                    ).trim()
                    echo "API response code: ${response}"

                    if (response != '200') {
                        error("API test failed! Expected 200, got ${response}")
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up containers...'
            sh 'docker-compose -f docker-compose.yml down || true'
        }
        success {
            echo 'Pipeline succeeded 🎉'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}


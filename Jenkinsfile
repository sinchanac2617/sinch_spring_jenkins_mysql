pipeline {
    agent any

    environment {
        APP_PORT = "8088"
        LOG_FILE = "app.log"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
            post {
                success { echo "Checkout completed successfully" }
                failure { echo "Checkout FAILED" }
            }
        }

        stage('Build') {
            steps {
                sh "./mvnw clean package -DskipTests"
            }
            post {
                success { echo "Build successful" }
                failure { echo "Build FAILED" }
            }
        }

        stage('Stop Existing Process') {
            steps {
                script {
                    def pid = sh(script: "lsof -t -i:${APP_PORT} || true", returnStdout: true).trim()

                    if (pid) {
                        echo "Killing PID ${pid}"
                        sh "kill -9 ${pid}"
                    } else {
                        echo "No app running on port ${APP_PORT}"
                    }
                }
            }
            post {
                success { echo "Stop Existing Process completed" }
                failure { echo "Stop Existing Process FAILED" }
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    nohup java -jar target/*.jar --server.port=${APP_PORT} > ${LOG_FILE} 2>&1 &
                """
            }
            post {
                success { echo "Application deployed successfully" }
                failure { echo "Deployment FAILED" }
            }
        }
    }

    post {
        always { echo "Pipeline Finished" }
    }
}

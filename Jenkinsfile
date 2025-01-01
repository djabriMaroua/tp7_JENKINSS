pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/djabriMaroua/TP_7_OGL.git'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                script {
                    try {
                        bat './gradlew test'
                        junit '**/build/test-results/test/*.xml'
                    } catch (Exception e) {
                        echo "Test stage failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("Test stage failed")
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                script {
                    try {
                        bat './gradlew build'
                        archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                    } catch (Exception e) {
                        echo "Build stage failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("Build stage failed")
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
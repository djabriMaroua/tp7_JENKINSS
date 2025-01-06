pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://localhost:9000/'
    }






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

        stage('Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                script {
                    try {
                        // Ensure the system clock is correct
                        def currentDate = new Date()
                        echo "Current system date: ${currentDate}"

                        // Wrap SonarQube analysis within the required block
                        withSonarQubeEnv('sonar') { // Replace 'sonar' with your SonarQube server configuration name
                            bat "./gradlew sonarqube -Dsonar.host.url=${SONAR_HOST_URL}"
                        }
                    } catch (Exception e) {
                        echo "SonarQube analysis failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("SonarQube analysis failed")
                    }
                }
            }
        }

        stage('Code Quality') {
            steps {
                echo 'Checking SonarQube Quality Gates...'
                script {
                    try {
                        timeout(time: 20, unit: 'MINUTES') { // Adjust timeout as needed
                            def qg = waitForQualityGate()
                            if (qg.status != 'OK') {
                                echo "Quality Gates failed: ${qg.status}"
                                currentBuild.result = 'FAILURE'
                                error("Quality Gates failed. Stopping pipeline.")
                            } else {
                                echo "Quality Gates passed: ${qg.status}"
                            }
                        }
                    } catch (Exception e) {
                        echo "Quality Gates check failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("Quality Gates check failed")
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

        stage('Deploy') {
            steps {
                echo 'Deploying to MyMavenRepo...'
                bat "./gradlew publish"
            }
        }

        stage('Send Notification') {
            steps {
                script {
                    def result = currentBuild.result
                    if (result == 'SUCCESS') {
                        mail to: 'lm_djabri@esi.dz',
                             subject: "Jenkins Build #${env.BUILD_NUMBER} Success",
                             body: "The build #${env.BUILD_NUMBER} was successful.\n\nCheck it out: ${env.BUILD_URL}"
                    } else {
                        mail to: 'lm_djabri@esi.dz',
                             subject: "Jenkins Build #${env.BUILD_NUMBER} Failure",
                             body: "The build #${env.BUILD_NUMBER} failed.\n\nCheck it out: ${env.BUILD_URL}"
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
pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://197.140.142.82:9000'
        SONAR_LOGIN = credentials('mytoken')
        MAVEN_REPO_USERNAME = credentials('myMavenRepo')
        MAVEN_REPO_PASSWORD = credentials('maroua2003')
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
                bat './gradlew test'
                junit '**/build/test-results/test/*.xml'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                withSonarQubeEnv('SonarQube') {
                    bat "./gradlew sonarqube"
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                bat './gradlew build'
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to MyMavenRepo...'
                bat "./gradlew publish"
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
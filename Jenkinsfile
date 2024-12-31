pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://197.140.142.82:9000'
        SONAR_LOGIN = credentials('mytoken') // Configurez un credential ID dans Jenkins pour le token SonarQube
        MAVEN_REPO_USERNAME = credentials('myMavenRepo') // Configurez un credential ID
        MAVEN_REPO_PASSWORD = credentials('maroua2003') // Configurez un credential ID
    }

    stages {

        stage('Checkout') {
            steps {
                // Récupérer le code depuis GitHub
                git branch: 'main', url: 'https://github.com/djabriMaroua/TP_7_OGL.git'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                // Lancer les tests
                bat './gradlew test'
                // Archiver les résultats des tests
                junit 'build/test-results/test/*.xml'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                // Analyser le code avec SonarQube
                bat "./gradlew sonarqube -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_LOGIN}"
            }
        }

        stage('Code Quality') {
            steps {
                echo 'Checking SonarQube Quality Gates...'
                script {
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Quality Gate failed: ${qualityGate.status}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                // Compiler le projet et générer un fichier JAR
                bat './gradlew build'
                // Archiver les artefacts
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to MyMavenRepo...'
                // Déployer le JAR sur MyMavenRepo
                bat "./gradlew publish -Drepo.username=${MAVEN_REPO_USERNAME} -Drepo.password=${MAVEN_REPO_PASSWORD}"
            }
        }

        stage('Notification') {
            steps {
                echo 'Sending notifications...'
                mail to: 'lm_djabri@esi.dz',
                     subject: "Build ${currentBuild.fullDisplayName} - ${currentBuild.result}",
                     body: "Pipeline execution is complete. Status: ${currentBuild.result}."
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        failure {
            echo 'Pipeline failed!'
            mail to: 'lm_djabri@esi.dz',
                 subject: "Build ${currentBuild.fullDisplayName} - FAILED",
                 body: "Pipeline failed at stage: ${currentBuild.currentStage}."
        }
    }
}

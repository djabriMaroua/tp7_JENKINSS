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
                junit 'build/test-results/test/*.xml'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                bat """
                    ./gradlew sonarqube \
                    -Dsonar.host.url=${SONAR_HOST_URL} \
                    -Dsonar.login=${SONAR_LOGIN}
                """
            }
        }

        stage('Code Quality') {
            steps {
                echo 'Checking SonarQube Quality Gates...'
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
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
                bat """
                    ./gradlew publish \
                    -Drepo.username=${MAVEN_REPO_USERNAME} \
                    -Drepo.password=${MAVEN_REPO_PASSWORD}
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'Pipeline succeeded!'
            emailext (
                to: 'lm_djabri@esi.dz',
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                mimeType: 'text/html'
            )
        }
        failure {
            echo 'Pipeline failed!'
            emailext (
                to: 'lm_djabri@esi.dz',
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                mimeType: 'text/html'
            )
        }
    }
}
pipeline {
  agent any
  stages {

    stage('Build') {
    tools {
                    jdk 'jdk 17' // Name as configured in Global Tool Configuration
                }
      steps {
        bat './gradlew sonar'
      }
    }

  }
}
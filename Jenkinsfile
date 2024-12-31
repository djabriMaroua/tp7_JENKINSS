pipeline {
  agent any
  stages {

    stage('Build') {
    tools {
                    jdk 'jdk 11' // Name as configured in Global Tool Configuration
                }
      steps {
        bat './gradlew sonar'
      }
    }

  }
}
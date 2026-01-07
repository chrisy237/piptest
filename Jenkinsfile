pipeline {
  agent any

  environment {
    SONAR_ENV = 'sonarqube'     // must match Manage Jenkins -> System -> SonarQube servers -> Name
    SONAR_PROJECT_KEY = 'piptest'
    SONAR_PROJECT_NAME = 'piptest'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('SonarQube Scan') {
      steps {
        script {
          // MUST match Manage Jenkins -> Tools -> SonarQube Scanner installations -> Name
          def scannerHome = tool 'SonarScanner'

          withSonarQubeEnv("${SONAR_ENV}") {
            sh """
              "${scannerHome}/bin/sonar-scanner" \
                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                -Dsonar.projectName=${SONAR_PROJECT_NAME} \
                -Dsonar.sources=. \
                -Dsonar.exclusions=**/node_modules/**,**/dist/**,**/build/**,**/.git/**
            """
          }
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 10, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
  }
}

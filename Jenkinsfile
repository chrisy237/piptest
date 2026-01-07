pipeline {
  agent any

  environment {
    SONAR_ENV = 'sonarqube'
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

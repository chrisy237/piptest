pipeline {
  agent any

  environment {
    SONAR_ENV = 'sonarqube'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('SonarQube Scan') {
      steps {
        withSonarQubeEnv("${SONAR_ENV}") {
          sh """
            sonar-scanner \
              -Dsonar.projectKey=myproject \
              -Dsonar.projectName=myproject \
              -Dsonar.sources=. \
              -Dsonar.exclusions=**/node_modules/**,**/dist/**,**/build/**,**/.git/**
          """
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

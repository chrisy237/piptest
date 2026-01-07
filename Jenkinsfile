pipeline {
  agent any

  tools {
    hudson.plugins.sonar.SonarRunnerInstallation 'SonarScanner'
  }

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
              -Dsonar.projectKey=piptest \
              -Dsonar.projectName=piptest \
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

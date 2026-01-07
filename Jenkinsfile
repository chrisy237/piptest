pipeline {
  agent any

  environment {
    // MUST match: Jenkins -> Manage Jenkins -> System -> SonarQube servers -> Name
    SONAR_ENV = 'sonarqube'

    // Project info (must match what you want in SonarQube)
    SONAR_PROJECT_KEY = 'piptest'
    SONAR_PROJECT_NAME = 'piptest'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('SonarQube Scan') {
      steps {
        script {
          // MUST match: Jenkins -> Manage Jenkins -> Tools -> SonarQube Scanner installations -> Name
          def scannerHome = tool 'SonarScanner'

          withSonarQubeEnv("${SONAR_ENV}") {
            // Fail fast if token is not injected (does NOT reveal the token)
            sh '''
              if [ -z "$SONAR_AUTH_TOKEN" ]; then
                echo "ERROR: SONAR_AUTH_TOKEN is missing."
                echo "Fix: Jenkins -> Manage Jenkins -> System -> SonarQube servers -> select Server authentication token."
                exit 2
              fi
            '''

            sh """
              "${scannerHome}/bin/sonar-scanner" \
                -Dsonar.login=$SONAR_AUTH_TOKEN \
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

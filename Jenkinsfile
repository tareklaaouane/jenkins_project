pipeline {
  agent any

  environment {
    APP_NAME    = "myapp"
    DEPLOY_DIR  = "/home/lucifer/Deploy"
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/tareklaaouane/jenkins_project.git'
      }
    }

    stage('Build') {
      steps {
        sh 'mvn -B clean package -DskipTests'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar,target/*.war', fingerprint: true
        }
      }
    }

    stage('Test') {
      steps {
        sh 'mvn -B test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }

    stage('Deploy (Local)') {
      steps {
        sh """
          set -e
          echo "Deploying locally to: /home/lucifer/Deploy"
          mkdir -p /home/lucifer/Deploy

          # Copy JAR/WAR to deploy directory
          if ls target/*.jar 1> /dev/null 2>&1; then
            cp -v target/*.jar /home/lucifer/Deploy/
          elif ls target/*.war 1> /dev/null 2>&1; then
            cp -v target/*.war /home/lucifer/Deploy"
          else
            echo "No JAR/WAR found in target/"
            exit 1
          fi

          echo "Local deploy done."
        """
      }
    }
  }

  post {
    success { echo "Local CI/CD finished successfully." }
    failure { echo "❌ Pipeline failed. Check stage logs." }
  }
}

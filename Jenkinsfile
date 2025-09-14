pipeline {
  agent any

  // poll every ~2 minutes
  triggers { pollSCM('H/2 * * * *') }

  environment {
    MAIL_TO = 'pahalavankandeepan@gmail.com'   // Mailtrap will accept any address
  }

  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Build') { steps { bat 'echo Build: compile/package' } }

    stage('Run Tests') {
      steps { bat 'echo Running unit + integration tests' }
      post {
        always {
          emailext(
            to: env.MAIL_TO,
            subject: "${env.JOB_NAME} #${env.BUILD_NUMBER} - Run Tests: ${currentBuild.currentResult}",
            body: "Job: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\nStage: Run Tests\nStatus: ${currentBuild.currentResult}\n\nConsole: ${env.BUILD_URL}console\n",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('Code Analysis') { steps { bat 'echo Static analysis (Sonar etc.)' } }

    stage('NPM Audit (Security Scan)') {
      steps { bat 'echo npm audit' }
      post {
        always {
          emailext(
            to: env.MAIL_TO,
            subject: "${env.JOB_NAME} #${env.BUILD_NUMBER} - NPM Audit: ${currentBuild.currentResult}",
            body: "Job: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\nStage: NPM Audit (Security Scan)\nStatus: ${currentBuild.currentResult}\n\nConsole: ${env.BUILD_URL}console\n",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('Deploy to Staging') { steps { bat 'echo Deploy artifact to staging' } }
    stage('Integration Tests on Staging') { steps { bat 'echo E2E/smoke on staging' } }
    stage('Deploy to Production') { steps { bat 'echo Promote artifact to prod' } }
  }
}

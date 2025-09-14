pipeline {
  agent any

  // Poll every ~2 minutes so Jenkins auto-builds
  triggers { pollSCM('H/2 * * * *') }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps { bat 'echo Build: compile/package' }
    }

    stage('Run Tests') {
      steps { bat 'echo Running unit + integration tests' }
      post {
        always {
          // Force-send to this address (no filtering), attach build log
          emailext(
            to: 'pahalavankandeepan@gmail.com',
            recipientProviders: [],   // IMPORTANT: avoid plugin filtering
            subject: "Run Tests finished - Build #${env.BUILD_NUMBER}",
            body: """Stage: Run Tests
Build: #${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}
Job: ${env.JOB_NAME}
URL: ${env.BUILD_URL}""",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('Code Analysis') {
      steps { bat 'echo Static analysis (Sonar etc.)' }
    }

    stage('NPM Audit (Security Scan)') {
      steps { bat 'echo npm audit' }
      post {
        always {
          // Second email with different subject
          emailext(
            to: 'pahalavankandeepan@gmail.com',
            recipientProviders: [],   // IMPORTANT
            subject: "Security Scan finished - Build #${env.BUILD_NUMBER}",
            body: """Stage: Security Scan
Build: #${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}
Job: ${env.JOB_NAME}
URL: ${env.BUILD_URL}""",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('Deploy to Staging') {
      steps { bat 'echo Deploy artifact to staging' }
    }

    stage('Integration Tests on Staging') {
      steps { bat 'echo E2E/smoke on staging' }
    }

    stage('Deploy to Production') {
      steps { bat 'echo Promote artifact to prod' }
    }
  }
}

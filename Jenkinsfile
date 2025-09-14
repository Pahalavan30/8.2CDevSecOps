pipeline {
  agent any

  // Auto-build every ~2 minutes via polling (no webhooks needed)
  triggers { pollSCM('H/2 * * * *') }

  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Build')                        { steps { bat 'echo Build: compile/package' } }
    stage('Unit & Integration Tests')     { steps { bat 'echo Run unit + integration tests' } }
    stage('Code Analysis')                { steps { bat 'echo Static analysis (Sonar etc.)' } }
    stage('Security Scan')                { steps { bat 'echo Dependency/SAST scan' } }
    stage('Deploy to Staging')            { steps { bat 'echo Deploy artifact to staging' } }
    stage('Integration Tests on Staging') { steps { bat 'echo E2E/smoke on staging' } }
    stage('Deploy to Production')         { steps { bat 'echo Promote artifact to prod' } }
  }
}

post {
    success {
      mail to: '6381540311-e8e543+user1@inbox.mailtrap.io',
           subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
           body: """Build succeeded.

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
URL:   ${env.BUILD_URL}
"""
    }
    failure {
      mail to: '6381540311-e8e543+user1@inbox.mailtrap.io',
           subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
           body: """Build failed.

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Console: ${env.BUILD_URL}console
"""
    }
  }
}
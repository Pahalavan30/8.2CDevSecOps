pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }

  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Build') { steps { bat 'echo Build: compile/package' } }

    stage('Run Tests') {
      steps { bat 'echo Running unit + integration tests' }
      post {
        always {
          emailext(
            to: 'pahalavankandeepan@gmail.com',   // <-- your email here
            recipientProviders: [],               // don’t auto-add recipients
            subject: "Run Tests – ${env.JOB_NAME} #${env.BUILD_NUMBER} – ${currentBuild.currentResult}",
            body: """Stage: Run Tests
Build: #${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}
URL: ${env.BUILD_URL}
""",
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
          sleep time: 3, unit: 'SECONDS'         // small gap so second mail isn’t deduped
          emailext(
            to: 'pahalavankandeepan@gmail.com',  // <-- same email is fine
            recipientProviders: [],
            subject: "Security Scan – ${env.JOB_NAME} #${env.BUILD_NUMBER} – ${currentBuild.currentResult}",
            body: """Stage: Security Scan
Build: #${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}
URL: ${env.BUILD_URL}
""",
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



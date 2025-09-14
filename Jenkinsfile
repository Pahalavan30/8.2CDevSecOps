pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }

  // >>> change this if you want to use Gmail instead of Mailtrap
  //     (but then add gmail.com to Allowed Domains in Jenkins)
  environment {
    MAIL_TO = 'test@mailtrap.io'
  }

  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Build') { steps { bat 'echo Build: compile/package' } }

    stage('Run Tests') {
      steps { bat 'echo Running unit + integration tests' }
      post {
        always {
          script {
            def stamp = new Date().format("yyyy-MM-dd HH:mm:ss")
            emailext(
              to: env.MAIL_TO,
              subject: "Run Tests finished - ${env.JOB_NAME} #${env.BUILD_NUMBER} @ ${stamp}",
              body: """Run Tests finished for ${env.JOB_NAME} #${env.BUILD_NUMBER}
Result: ${currentBuild.currentResult}
Log: ${env.BUILD_URL}console""",
              attachLog: true,
              compressLog: true,
              mimeType: 'text/plain'
            )
          }
        }
      }
    }

    stage('Code Analysis') { steps { bat 'echo Static analysis (Sonar etc.)' } }

    stage('NPM Audit (Security Scan)') {
      steps { bat 'echo npm audit' }
      post {
        always {
          script {
            sleep(time: 5, unit: 'SECONDS')   // small gap to avoid any SMTP de-dupe
            def stamp = new Date().format("yyyy-MM-dd HH:mm:ss")
            emailext(
              to: env.MAIL_TO,
              subject: "Security Scan finished - ${env.JOB_NAME} #${env.BUILD_NUMBER} @ ${stamp}",
              body: """Security Scan finished for ${env.JOB_NAME} #${env.BUILD_NUMBER}
Result: ${currentBuild.currentResult}
Log: ${env.BUILD_URL}console""",
              attachLog: true,
              compressLog: true,
              mimeType: 'text/plain'
            )
          }
        }
      }
    }

    stage('Deploy to Staging')            { steps { bat 'echo Deploy artifact to staging' } }
    stage('Integration Tests on Staging') { steps { bat 'echo E2E/smoke on staging' } }
    stage('Deploy to Production')         { steps { bat 'echo Promote artifact to prod' } }
  }
}




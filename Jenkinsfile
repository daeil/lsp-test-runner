pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh '''echo "Download & Build"
export BUILD_RESULT=123'''
      }
    }

    stage('Composite Test') {
      steps {
        catchError() {
          sh '''echo "composite tests"
export '''
        }

      }
    }

    stage('Single Test') {
      steps {
        catchError() {
          sh '''echo "single tests"
exit -1'''
        }

      }
    }

    stage('Send Reports') {
      parallel {
        stage('Send Reports') {
          steps {
            emailext(subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', attachLog: true, body: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:  Check console output at $BUILD_URL to view the results.', compressLog: true, saveOutput: true, to: 'dean.kwon@windriver.com', from: 'Jenkins')
          }
        }

        stage('error') {
          steps {
            mail(subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', body: 'Email: SUCCESS:$BUILD_RESULT', to: 'dean.kwon@windriver.com')
          }
        }

      }
    }

  }
  triggers {
    cron('H(30-50) 11 * * 1-5')
  }
}
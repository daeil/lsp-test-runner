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
      steps {
        emailext(subject: '${DEFAULT_SUBJECT}', attachLog: true, body: '${DEFAULT_CONTENT}', compressLog: true, saveOutput: true, to: 'dean.kwon@windriver.com', from: 'Jenkins')
      }
    }

  }
  environment {
    BUILD_RESULT = '0'
    COMPOSITE_TEST_RESULT = '0'
    SINGLE_TEST_RESULT = '0'
  }
  triggers {
    cron('H(30-50) 11 * * 1-5')
  }
}
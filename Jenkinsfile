pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        script {
          env.BUILD_RESULT=sh(script:"echo 'Download & Build' BUILD_RESULT=123")
        }
      }
    }

    stage('Composite Test') {
      steps {
        catchError() {
          sh '''echo "composite tests"
          COMPOSITE_TEST_RESULT="SUCCESS" '''
        }
      }
    }

    stage('Single Test') {
      steps {
        catchError() {
          script {
            env.BUILD_DATE = sh(returnStdout: true, script: "date -u +'%Y-%m-%dT%H:%M:%SZ'")
            sh '''echo "single tests"
                   export SINGLE_TEST_RESULT=1 '''
          }
        }
      }
    }

    stage('Send Reports') {
      steps {
        emailext(subject: '${DEFAULT_SUBJECT}', attachLog: true, body: '${DEFAULT_CONTENT}\nBUILD_RESULT=${BUILD_RESULT}, COMPOSITE_TEST_RESULT=${COMPOSITE_TEST_RESULT}, SINGLE_TEST_RESULT=${SINGLE_TEST_RESULT}', compressLog: true, saveOutput: true, to: 'dean.kwon@windriver.com', from: 'Jenkins')
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

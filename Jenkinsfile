pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh '''rm -rf ${HOME}/lsp-dev-nightly-test;
mkdir -p ${HOME}/lsp-dev-nightly-test;
cd ${HOME}/lsp-dev-nightly-test;

repo init -u http://sel-lsp-gerrit.wrs.com:8080/lsp_manifest
repo sync
cd wrs/jenkins/lsp-test-runner

make docker-clean
make docker-make-image
make docker-build-lsp'''
      }
    }

    stage('Run Test') {
      steps {
        catchError() {
          sh '''cd ${HOME}/lsp-dev-nightly-test/wrs/jenkins/lsp-test-runner
make docker-run-composite-test
'''
          sh '''cd ${HOME}/lsp-dev-nightly-test/wrs/jenkins/lsp-test-runner
make docker-run-unit-test'''
        }

      }
    }

    stage('Equivalence Test') {
      steps {
        warnError(message: 'Equivalence Test') {
          sh '''cd ${HOME}/lsp-dev-nightly-test/wrs/jenkins/lsp-test-runner
make docker-run-unit-test'''
        }

      }
    }

    stage('Send Report') {
      steps {
        sh 'BUILD_RESULT=${BUILD_RESULT}, COMPOSITE_TEST_RESULT=${COMPOSITE_TEST_RESULT}, SINGLE_TEST_RESULT=${SINGLE_TEST_RESULT}'
        emailext(subject: '${DEFAULT_SUBJECT}', body: '${DEFAULT_CONTENT}', to: 'dean.kwon@windriver.com')
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
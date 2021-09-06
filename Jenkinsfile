pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh '''sudo rm -rf ${HOME}/lsp-dev-nightly-test;
mkdir -p ${HOME}/lsp-dev-nightly-test;
cd ${HOME}/lsp-dev-nightly-test;

repo init -u http://sel-lsp-gerrit.wrs.com:8080/lsp_manifest
repo sync
cd wrs/jenkins/lsp-test-runner

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
make docker-run-equiv-test'''
          sh '''cd ${HOME}/lsp-dev-nightly-test/lsp/
repo download -c lsp 1348

cd tool/profile

# ensure to stop containers
make docker-clean

make docker-make-image

# BUILD
make docker-build-lsp LSP_BRANCH=$CHECKOUT_BRANCH

# RUN PROFILING
make docker-run-lsp LOG_FILE=log_210224_111146_FRLSP.bin
make docker-run-lsp LOG_FILE=log_210302_113433_FRLSP.bin
make docker-run-lsp LOG_FILE=log_210311_134630_FRLSP.bin
'''
        }

      }
    }

    stage('Send Report') {
      steps {
        sh 'BUILD_RESULT=${BUILD_RESULT}, COMPOSITE_TEST_RESULT=${COMPOSITE_TEST_RESULT}, SINGLE_TEST_RESULT=${SINGLE_TEST_RESULT}'
        emailext(subject: '${DEFAULT_SUBJECT}', body: '${DEFAULT_CONTENT}', to: 'dean.kwon@windriver.com')
      }
    }

    stage('Clean up') {
      steps {
        sh '''cd ${HOME}/lsp-dev-nightly-test/wrs/jenkins/lsp-test-runner
make docker-clean
'''
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, cleanupMatrixParent: true, deleteDirs: true, disableDeferredWipeout: true)
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
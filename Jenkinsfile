pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh '''repo init -u http://sel-lsp-gerrit.wrs.com:8080/lsp_manifest
repo sync
cd wrs/jenkins/lsp-test-runner

make docker-clean
make docker-make-image
make docker-prepare-lsp
'''
      }
    }

    stage('CompositeTest') {
      steps {
        catchError() {
          sh '''cd wrs/jenkins/lsp-test-runner
make docker-run-composite-test
'''
        }

      }
    }

    stage('UnitTest') {
      steps {
        catchError() {
          sh '''cd wrs/jenkins/lsp-test-runner
make docker-run-unit-test'''
        }

      }
    }

    stage('Equivalence') {
      steps {
        warnError(message: 'Equivalence Test') {
          sh '''cd wrs/jenkins/lsp-test-runner
make docker-run-equiv-test'''
        }

      }
    }

    stage('WCET') {
      steps {
        warnError(message: 'WCET Test') {
          sh '''# BUILD

cd wrs/jenkins/wcet-checker
make docker-clean
make docker-build-lsp LSP_BRANCH=wr_dev

'''
          sh '''# log_210916_102616
cd wrs/jenkins/wcet-checker
make docker-run-lsp LOG_FILE=log_210916_102616.bin
'''
          sh '''# log_210916_102916

cd wrs/jenkins/wcet-checker
make docker-run-lsp LOG_FILE=log_210916_102916.bin
'''
          sh '''# log_211005_160201

cd wrs/jenkins/wcet-checker
make docker-run-lsp LOG_FILE=log_211005_160201.bin'''
        }

      }
    }

    stage('Send Report') {
      steps {
        sh 'BUILD_RESULT=${BUILD_RESULT}, COMPOSITE_TEST_RESULT=${COMPOSITE_TEST_RESULT}, SINGLE_TEST_RESULT=${SINGLE_TEST_RESULT}'
      }
    }

    stage('Clean up') {
      steps {
        sh '''cd wrs/jenkins/lsp-test-runner
make docker-clean

cd -

cd wrs/jenkins/wcet-checker
make docker-clean

cd -

sudo rm -rf lsp wrs'''
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
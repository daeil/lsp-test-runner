pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh '''rm -rf ${HOME}/lsp-dev-nightly-test;
mkdir -p ${HOME}/lsp-dev-nightly-test;
docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev --net host jenkins bin/lsp-build.sh'''
      }
    }

    stage('Composite Test') {
      steps {
        catchError() {
          sh 'docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev --mount type=bind,source=${HOME}/data,target=/home/jenkins/lsp-dev/lsp/data/ jenkins bin/lsp-composite-test.sh'
        }

      }
    }

    stage('Single Test') {
      steps {
        catchError() {
          sh 'docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev --mount type=bind,source=${HOME}/data,target=/home/jenkins/lsp-dev/lsp/data/ jenkins bin/lsp-single-test.sh'
        }

      }
    }

    stage('Send Report') {
      steps {
        sh 'BUILD_RESULT=${BUILD_RESULT}, COMPOSITE_TEST_RESULT=${COMPOSITE_TEST_RESULT}, SINGLE_TEST_RESULT=${SINGLE_TEST_RESULT}'
        emailext(to: 'LSP-WRS@windriver.com', saveOutput: true, body: '${DEFAULT_CONTENT}', subject: '${DEFAULT_SUBJECT}', from: 'Jenkins')
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
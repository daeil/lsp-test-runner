pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh '''rm -rf ${HOME}/lsp-dev-nightly-test;
mkdir -p ${HOME}/lsp-dev-nightly-test;
docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev --add-host confluence.mobis.co.kr:192.168.224.4 jenkins bin/lsp-build.sh && ln -s  ${HOME}/data/* ${HOME}/lsp-dev-nightly-test/lsp/data'''
      }
    }

    stage('Composite Test') {
      steps {
        catchError() {
          sh 'docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev jenkins bin/lsp-composite-test.sh'
        }

      }
    }

    stage('Single Test') {
      steps {
        catchError() {
          sh 'docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev jenkins bin/lsp-single-test.sh'
        }

      }
    }

    stage('Send Reports') {
      steps {
        sh 'BUILD_RESULT=${BUILD_RESULT}, COMPOSITE_TEST_RESULT=${COMPOSITE_TEST_RESULT}, SINGLE_TEST_RESULT=${SINGLE_TEST_RESULT}'
        emailext(subject: '${DEFAULT_SUBJECT}', body: '${DEFAULT_CONTENT}', saveOutput: true, to: 'dean.kwon@windriver.com', from: 'Jenkins')
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
pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh '''rm -rf ${HOME}/lsp-dev-nightly-test;
mkdir -p ${HOME}/lsp-dev-nightly-test;
docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev --add-host confluence.mobis.co.kr:192.168.224.4 jenkins bin/lsp-build.sh'''
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
        emailext(subject: 'LSP Daily Test Rport', attachLog: true, body: 'LSP Daily Test Rport', compressLog: true, saveOutput: true, to: 'dean.kwon@windriver.com', from: 'Jenkins')
      }
    }

  }
  triggers {
    cron('H(30-50) 11 * * 1-5')
  }
}

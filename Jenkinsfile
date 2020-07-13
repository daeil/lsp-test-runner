pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh 'rm -rf ${HOME}/lsp-dev-nightly-test; mkdir -p ${HOME}/lsp-dev-nightly-test; docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev --add-host mgit.mobis.co.kr:192.168.224.5 jenkins bin/lsp-build.sh'
      }
    }

    stage('Composite Test') {
      steps {
        sh 'docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev jenkins bin/lsp-composite-test.sh'
      }
    }

    stage('Single Test') {
      steps {
        sh 'docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev jenkins bin/lsp-single-test.sh'
      }
    }

  }
  triggers {
    cron('H/30 11 * * 1 - 5')
  }
}

pipeline {
  agent any
  stages {
    stage('Download & Build') {
      steps {
        sh 'mkdir -p ${HOME}/lsp-dev-nightly-test; docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev-nightly-test --add-host mgit.mobis.co.kr:192.168.224.5 jenkins bin/lsp-build.sh'
      }
    }

    stage('Test') {
      steps {
        sh 'docker run --rm -w /home/jenkins --user jenkins --mount type=bind,source=${HOME}/lsp-dev-nightly-test,target=/home/jenkins/lsp-dev-nightly-test jenkins bin/lsp-test.sh'
      }
    }

  }
  triggers {
    cron('H/30 11 * * *')
  }
}
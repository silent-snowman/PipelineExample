pipeline {
  agent none
  stages {
    stage('Build') {
      agent { label 'ubuntu' }
      steps {
        sh './build.sh'
        archiveArtifacts artifacts: 'output.bin'
        stash 'output.bin'
      }
    }
    stage('Test') {
      agent { label 'build && x64' }
      steps {
        unstash 'output.bin'
        sh './test.sh'
        stash 'output2.bin'
      }
    }
    stage('Merge') {
      agent { label 'master' }
      steps {
        step([$class: 'WsCleanup'])
        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: '681c55dd-3c24-4009-a0b5-70a52055b95f', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD']]) {
          sh("git config credential.username ${env.GIT_USERNAME}")
          sh("git config credential.helper '!echo password=\$GIT_PASSWORD; echo'")
          sh("GIT_ASKPASS=true git push origin --tags")
        }
      }
    }
  }
}

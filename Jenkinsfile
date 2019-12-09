def mergeBack = false
def targetBranch = 'master'

node('docker') {
  stage('checkout') {
    deleteDir()
    checkout scm
    if(env.BRANCH_NAME =~ "ready/*") {
      mergeBack = true
//      withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'pass', usernameVariable: 'user')]) {
//        sh "echo machine github.com login ${user} password ${pass} > ~/.netrc"
//        sh "chmod 0600 ~/.netrc"
//      }
      sh "git config remote.origin.fetch \"+refs/heads/*:refs/remotes/origin/*\""
      sh "git fetch origin ${targetBranch}"
      sh "git checkout -b ${targetBranch} origin/${targetBranch}"
      sh "git merge --ff-only origin/${env.BRANCH_NAME}"
    }
  }
  stage('build') {
    sh '''./build.sh'''
  }
  stage('test') {
    sh '''./test.sh'''
  }
//  stage('archive') {
//    archiveArtifacts 'out/**/*.*'
//  }
  stage('push ?'){
    if(mergeBack){
      sh "git push origin ${targetBranch}"
      sh "git push origin :${env.BRANCH_NAME}"
      currentBuild.description = "Merged ${env.BRANCH_NAME}"
    } else {
      echo "Nothing to push to the server"
    }
  }
}

def label = "Flutter_v2020_05"

podTemplate(label: label, containers: [
  containerTemplate(name: 'flutter', image: 'cirrusci/flutter:stable', command: 'cat', ttyEnabled: true)
],
volumes: [
  hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
]) {
  node(label) {
    def myRepo = checkout scm
    def gitCommit = myRepo.GIT_COMMIT
    def gitBranch = myRepo.GIT_BRANCH
    def shortGitCommit = "${gitCommit[0..10]}"
    def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
 
    stage('Run helm') {
      container('flutter') {
        sh "flutter doctor"
      }
    }
  }
}

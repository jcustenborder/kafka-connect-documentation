#!groovy

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10'))
])

node {
    deleteDir()
    checkout scm

    docker.image('jcustenborder/packaging-documentation').inside {
        sh 'make clean html'
        archiveArtifacts "_build/html/**/*"
    }
}
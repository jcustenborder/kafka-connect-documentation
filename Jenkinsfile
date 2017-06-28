#!groovy

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10'))
])

node {
    deleteDir()
    checkout scm

    docker.image('python').inside {
        sh 'pip install -r requirements.txt'
        sh 'make clean html'
        archiveArtifacts "_build/html/**/*"
    }
}
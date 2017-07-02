#!groovy

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10'))
])

def copyArtifacts(String name, String projectName) {
    def connectorRoot = "${pwd()}/connectors"
    def artifactRoot = "${pwd()}/_temp"

    def artifactDirectory = "${artifactRoot}/${name}"
    def connectorDirectory = "${connectorRoot}/${name}"

    step ([$class: 'CopyArtifact',
        projectName: projectName,
        filter: 'target/docs/**/*',
        target: "${artifactDirectory}"
    ]);

    sh "cp -rv '${artifactDirectory}/target/docs' '${connectorDirectory}'"

}


node {
    deleteDir()
    checkout scm

    stage('copy') {
        copyArtifacts('kafka-connect-syslog', 'jcustenborder/kafka-connect-syslog/rst')
    }

    stage('build') {
        docker.image('jcustenborder/packaging-documentation').inside {
            sh 'make clean html'
            archiveArtifacts "_build/html/**/*"
        }
    }

    stage('publish') {
        sh 'mkdir _build/gh-pages'
        dir('_build/gh-pages') {
            git branch: 'gh-pages', changelog: false, credentialsId: '2763d26b-c6c4-4e58-afda-af8999422a09', poll: false, url: 'git@github.com:jcustenborder/kafka-connect-documentation.git'
            sh 'cp -rv ../html/ .'
            sh 'git add .'
            sh "git commit -m 'Build ${BUILD_NUMBER}'"
            sh "git push 'git@github.com:jcustenborder/kafka-connect-documentation.git' gh-pages"
        }
    }
}
#!groovy

properties([
    buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '30')),
    disableConcurrentBuilds(),
    pipelineTriggers([
        upstream(threshold: 'SUCCESS', upstreamProjects: projects.connectors.values().join(','))
    ])
])

def copyArtifacts(String name, String projectName) {
    def connectorRoot = "${pwd()}/projects"
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
        connectors.each { key, value ->
            copyArtifacts(key, value)
        }
    }

    stage('build') {
        docker.image('jcustenborder/packaging-documentation:52').inside {
            sh 'make clean html'
            archiveArtifacts "_build/html/**/*"
        }
    }

    stage('publish') {
        sh 'mkdir _build/gh-pages'
        dir('_build/gh-pages') {
            git branch: 'gh-pages', changelog: false, credentialsId: '50a4ec3a-9caf-43d1-bfab-6465b47292da', poll: false, url: 'git@github.com:jcustenborder/kafka-connect-documentation.git'
            sh 'rsync --exclude ".git" -avz --delete ../html/* .'
            sh 'git config user.email "jenkins@custenborder.com"'
            sh 'git config user.name "Jenkins"'
            sh "echo `git add --all . && git commit -m 'Build ${BUILD_NUMBER}' .`"
            sshagent (credentials: ['50a4ec3a-9caf-43d1-bfab-6465b47292da']) {
                sh "git push 'git@github.com:jcustenborder/kafka-connect-documentation.git' gh-pages"
            }
        }
    }
}

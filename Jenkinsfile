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

    def introductionFile = "${connectorDirectory}/introduction.rst"


node {
    deleteDir()
    checkout scm

    includeJobs = [
        'jcustenborder/kafka-connect-syslog/rst'
    ]

    stage('copy') {
        copyArtifacts('kafka-connect-syslog', 'jcustenborder/kafka-connect-syslog/rst')
    }

    docker.image('jcustenborder/packaging-documentation').inside {
        stage('build') {
            sh 'make clean html'
        }

        archiveArtifacts "_build/html/**/*"
    }
}
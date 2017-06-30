#!groovy

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10'))
])

def copyArtifacts(String name, String projectName) {
    def connectorRoot = "${pwd()}/connectors"
    def artifactRoot = '${pwd()}/_temp'
    mkdir '_temp'
    mkdir $artifactRoot

    def artifactDirectory = "${artifactRoot}/${name}"
    def connectorDirectory = "${connectorRoot}/${name}"
    mkdir ${connectorDirectory}

    step ([$class: 'CopyArtifact',
        projectName: projectName,
        filter: 'target/docs/**/*',
        target: ${artifactDirectory}
    ]);

    sh "cp -rv '${artifactDirectory}' '${connectorDirectory}'"
}

node {
    deleteDir()
    checkout scm

    includeJobs = [
        'jcustenborder/kafka-connect-syslog/rst'
    ]

    copyArtifacts('kafka-connect-syslog', 'jcustenborder/kafka-connect-syslog/rst')

    docker.image('jcustenborder/packaging-documentation').inside {
        sh 'make clean html'
        archiveArtifacts "_build/html/**/*"
    }
}
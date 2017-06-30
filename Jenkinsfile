#!groovy

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10'))
])

node {
    deleteDir()
    checkout scm

    includeJobs = [
        'jcustenborder/kafka-connect-syslog/master'
    ]

    dir('connectors') {
        step ([$class: 'CopyArtifact',
            projectName: 'jcustenborder/kafka-connect-syslog/master',
            filter: 'target/docs/**/*']
        );
    }


    docker.image('jcustenborder/packaging-documentation').inside {
        sh 'make clean html'
        archiveArtifacts "_build/html/**/*"
    }
}
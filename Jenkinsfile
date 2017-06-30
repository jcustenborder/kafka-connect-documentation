#!groovy

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10'))
])

node {
    deleteDir()
    checkout scm

    includeJobs = [
        'jcustenborder/kafka-connect-syslog/rst'
    ]

    dir('connectors') {
        step ([$class: 'CopyArtifact',
            projectName: 'jcustenborder/kafka-connect-syslog/rst',
            filter: 'target/docs/**/*']
        );
    }


    docker.image('jcustenborder/packaging-documentation').inside {
        sh 'make clean html'
        archiveArtifacts "_build/html/**/*"
    }
}
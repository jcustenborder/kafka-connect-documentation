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
        copyArtifacts('kafka-connect-kinesis', 'jcustenborder/kafka-connect-kinesis/rst')
        copyArtifacts('kafka-connect-rabbitmq', 'jcustenborder/kafka-connect-rabbitmq/rst')
        copyArtifacts('kafka-connect-flume-avro', 'jcustenborder/kafka-connect-flume-avro/rst')
        copyArtifacts('kafka-connect-spooldir', 'jcustenborder/kafka-connect-spooldir/rst')
        copyArtifacts('kafka-connect-twitter', 'jcustenborder/kafka-connect-twitter/rst')
        copyArtifacts('kafka-connect-statsd', 'jcustenborder/kafka-connect-statsd/rst')
        copyArtifacts('kafka-connect-splunk', 'jcustenborder/kafka-connect-splunk/master')
        copyArtifacts('kafka-connect-solr', 'jcustenborder/kafka-connect-solr/rst')
        copyArtifacts('kafka-connect-snmp', 'jcustenborder/kafka-connect-snmp/rst')
        copyArtifacts('kafka-connect-simulator', 'jcustenborder/kafka-connect-simulator/rst')
        copyArtifacts('kafka-connect-salesforce', 'jcustenborder/kafka-connect-salesforce/rst')
        copyArtifacts('kafka-connect-influxdb', 'jcustenborder/kafka-connect-influxdb/rst')
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
            git branch: 'gh-pages', changelog: false, credentialsId: '50a4ec3a-9caf-43d1-bfab-6465b47292da', poll: false, url: 'git@github.com:jcustenborder/kafka-connect-documentation.git'
            sh 'cp -rv ../html/* .'
            sh 'git config user.email "jenkins@custenborder.com"'
            sh 'git config user.name "Jenkins"'
            sh "echo `git add . && git commit -m 'Build ${BUILD_NUMBER}'`"
            sshagent (credentials: ['50a4ec3a-9caf-43d1-bfab-6465b47292da']) {
                sh "git push 'git@github.com:jcustenborder/kafka-connect-documentation.git' gh-pages"
            }
        }
    }
}
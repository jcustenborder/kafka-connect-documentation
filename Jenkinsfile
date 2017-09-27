#!groovy

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')),
        pipelineTriggers([
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-syslog/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-kinesis/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-rabbitmq/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-flume-avro/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-spooldir/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-twitter/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-statsd/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-splunk/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-solr/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-snmp/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-simulator/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-salesforce/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-influxdb/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-maprdb/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-vertica/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-jms/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-transform-maxmind/master', threshold: hudson.model.Result.SUCCESS)
            upstream(upstreamProjects: 'jcustenborder/kafka-connect-transform-cef/master', threshold: hudson.model.Result.SUCCESS)
        ])
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
        copyArtifacts('kafka-connect-syslog', 'jcustenborder/kafka-connect-syslog/master')
        copyArtifacts('kafka-connect-kinesis', 'jcustenborder/kafka-connect-kinesis/master')
        copyArtifacts('kafka-connect-rabbitmq', 'jcustenborder/kafka-connect-rabbitmq/master')
        copyArtifacts('kafka-connect-flume-avro', 'jcustenborder/kafka-connect-flume-avro/master')
        copyArtifacts('kafka-connect-spooldir', 'jcustenborder/kafka-connect-spooldir/master')
        copyArtifacts('kafka-connect-twitter', 'jcustenborder/kafka-connect-twitter/master')
        copyArtifacts('kafka-connect-statsd', 'jcustenborder/kafka-connect-statsd/master')
        copyArtifacts('kafka-connect-splunk', 'jcustenborder/kafka-connect-splunk/master')
        copyArtifacts('kafka-connect-solr', 'jcustenborder/kafka-connect-solr/master')
        copyArtifacts('kafka-connect-snmp', 'jcustenborder/kafka-connect-snmp/master')
        copyArtifacts('kafka-connect-simulator', 'jcustenborder/kafka-connect-simulator/master')
        copyArtifacts('kafka-connect-salesforce', 'jcustenborder/kafka-connect-salesforce/master')
        copyArtifacts('kafka-connect-influxdb', 'jcustenborder/kafka-connect-influxdb/master')
        copyArtifacts('kafka-connect-maprdb', 'jcustenborder/kafka-connect-maprdb/master')
        copyArtifacts('kafka-connect-vertica', 'jcustenborder/kafka-connect-vertica/master')
        copyArtifacts('kafka-connect-jms', 'jcustenborder/kafka-connect-jms/master')
        copyArtifacts('kafka-connect-transform-maxmind', 'jcustenborder/kafka-connect-transform-maxmind/master')
        copyArtifacts('kafka-connect-transform-cef', 'jcustenborder/kafka-connect-transform-cef/master')
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
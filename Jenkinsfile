#!groovy

def upstream = [
        'kafka-connect-flume-avro':'jcustenborder/kafka-connect-flume-avro/master',
        'kafka-connect-influxdb':'jcustenborder/kafka-connect-influxdb/master',
        'kafka-connect-jms':'jcustenborder/kafka-connect-jms/master',
        'kafka-connect-kinesis':'jcustenborder/kafka-connect-kinesis/master',
        'kafka-connect-memcached':'jcustenborder/kafka-connect-memcached/master',
        'kafka-connect-rabbitmq':'jcustenborder/kafka-connect-rabbitmq/master',
        'kafka-connect-salesforce':'jcustenborder/kafka-connect-salesforce/master',
        'kafka-connect-simulator':'jcustenborder/kafka-connect-simulator/master',
        'kafka-connect-snmp':'jcustenborder/kafka-connect-snmp/master',
        'kafka-connect-solr':'jcustenborder/kafka-connect-solr/master',
        'kafka-connect-splunk':'jcustenborder/kafka-connect-splunk/master',
        'kafka-connect-spooldir':'jcustenborder/kafka-connect-spooldir/master',
        'kafka-connect-statsd':'jcustenborder/kafka-connect-statsd/master',
        'kafka-connect-syslog':'jcustenborder/kafka-connect-syslog/master',
        'kafka-connect-transform-cef':'jcustenborder/kafka-connect-transform-cef/master',
        'kafka-connect-transform-maxmind':'jcustenborder/kafka-connect-transform-maxmind/master',
        'kafka-connect-twitter':'jcustenborder/kafka-connect-twitter/master',
        'kafka-connect-vertica':'jcustenborder/kafka-connect-vertica/master'
]

def watchProjects = []

upstream.each { key, value ->
    watchProjects << value
}

properties([
    buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '30')),
    disableConcurrentBuilds(),
    pipelineTriggers([
        upstream(threshold: 'SUCCESS', upstreamProjects:  'jcustenborder/kafka-connect-flume-avro/master,jcustenborder/kafka-connect-influxdb/master,jcustenborder/kafka-connect-jms/master,jcustenborder/kafka-connect-kinesis/master,jcustenborder/kafka-connect-memcached/master,jcustenborder/kafka-connect-rabbitmq/master,jcustenborder/kafka-connect-salesforce/master,jcustenborder/kafka-connect-simulator/master,jcustenborder/kafka-connect-snmp/master,jcustenborder/kafka-connect-solr/master,jcustenborder/kafka-connect-splunk/master,jcustenborder/kafka-connect-spooldir/master,jcustenborder/kafka-connect-statsd/master,jcustenborder/kafka-connect-syslog/master,jcustenborder/kafka-connect-transform-cef/master,jcustenborder/kafka-connect-transform-maxmind/master,jcustenborder/kafka-connect-twitter/master,jcustenborder/kafka-connect-vertica/master')
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

    sh "echo ${watchProjects.join(',')}"

    stage('copy') {
/*#        upstream.each { key, value ->
#            copyArtifacts(key, value)
#        }
*/
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
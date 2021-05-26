#!groovy

def connectors = [
        'kafka-connect-memcached':'jcustenborder/kafka-connect-memcached/master',
        'kafka-connect-email':'jcustenborder/kafka-connect-email/master',
        'kafka-connect-flume-avro':'jcustenborder/kafka-connect-flume-avro/master',        
        'kafka-connect-redis':'jcustenborder/kafka-connect-redis/master',
        'kafka-connect-simulator':'jcustenborder/kafka-connect-simulator/master',
        'kafka-connect-snmp':'jcustenborder/kafka-connect-snmp/master',
        'kafka-connect-solr':'jcustenborder/kafka-connect-solr/master',
        'kafka-connect-splunk':'jcustenborder/kafka-connect-splunk/master',
        'kafka-connect-spooldir':'jcustenborder/kafka-connect-spooldir/master',
        'kafka-connect-opentsdb':'jcustenborder/kafka-connect-opentsdb/master',
        
        'kafka-connect-twitter':'jcustenborder/kafka-connect-twitter/master',
        'kafka-connect-aerospike':'jcustenborder/kafka-connect-aerospike/master',
        
        'kafka-connect-transform-archive':'jcustenborder/kafka-connect-transform-archive/master',
        'kafka-connect-json-schema':'jcustenborder/kafka-connect-json-schema/master',
        'kafka-connect-transform-cef':'jcustenborder/kafka-connect-transform-cef/master',
        'kafka-connect-transform-common':'jcustenborder/kafka-connect-transform-common/master',
        'kafka-connect-transform-cobol':'jcustenborder/kafka-connect-transform-cobol/master',
        'kafka-connect-transform-fix':'jcustenborder/kafka-connect-transform-fix/master',
        'kafka-connect-transform-maxmind':'jcustenborder/kafka-connect-transform-maxmind/master',
        'kafka-connect-transform-xml':'jcustenborder/kafka-connect-transform-xml/master',
        
        'kafka-config-provider-vault':'jcustenborder/kafka-config-provider-vault/master',
        'kafka-config-provider-aws':'jcustenborder/kafka-config-provider-aws/master',
        'kafka-config-provider-gcloud':'jcustenborder/kafka-config-provider-gcloud/master'
]

properties([
    buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '30')),
    disableConcurrentBuilds(),
    pipelineTriggers([
        upstream(threshold: 'SUCCESS', upstreamProjects: connectors.values().join(','))
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

#!groovy

def connectors = [
        'kafka-connect-memcached':'jcustenborder/kafka-connect-memcached/master',
        'kafka-connect-redis':'jcustenborder/kafka-connect-redis/master',
        'kafka-connect-simulator':'jcustenborder/kafka-connect-simulator/master',
        'kafka-connect-snmp':'jcustenborder/kafka-connect-snmp/master',
        'kafka-connect-solr':'jcustenborder/kafka-connect-solr/master',
        'kafka-connect-splunk':'jcustenborder/kafka-connect-splunk/master',
        'kafka-connect-spooldir':'jcustenborder/kafka-connect-spooldir/master',
        'kafka-connect-statsd':'jcustenborder/kafka-connect-statsd/master',
        'kafka-connect-twitter':'jcustenborder/kafka-connect-twitter/master',

        'kafka-connect-transform-archive':'jcustenborder/kafka-connect-transform-archive/master',
        'kafka-connect-transform-cef':'jcustenborder/kafka-connect-transform-cef/master',
        'kafka-connect-transform-common':'jcustenborder/kafka-connect-transform-common/master',
        'kafka-connect-transform-maxmind':'jcustenborder/kafka-connect-transform-maxmind/master',
        'kafka-connect-transform-xml':'jcustenborder/kafka-connect-transform-xml/master'
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

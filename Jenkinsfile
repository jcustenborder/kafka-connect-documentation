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

    if(fileExists(introductionFile)) {
        writeFile(
            file: "${introductionFile}",
            text: "==============
${name}
==============

.. toctree::
   :maxdepth: 1
   :caption: Source Connectors:
   :glob:

   sources/*


.. toctree::
   :maxdepth: 1
   :caption: Sink Connectors:
   :glob:

   sinks/*


.. toctree::
   :maxdepth: 1
   :caption: Transformations:
   :glob:

   transformations/*


.. toctree::
   :maxdepth: 0
   :caption: Schemas:
   :
   :glob:
   :hidden:

   schemas/*
"
    }

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
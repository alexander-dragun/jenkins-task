def artifactory_name = 'Artifactory'
def server = Artifactory.server artifactory_name
def buildInfo = Artifactory.newBuildInfo()
def repo_url = 'https://github.com/curl/curl.git'
def repo_branch = 'master'

properties([[$class: 'BuildDiscarderProperty',
                strategy: [$class: 'LogRotator', numToKeepStr: '5']],
                pipelineTriggers([cron('H */24 * * 1-5')]),
                ])

node('Agent') {
    stage('Get project') {
        git branch: repo_branch, url: repo_url
    }

    stage('Build') {
        sh './buildconf && ./configure --enable-debug --prefix=/`pwd`/curl_app'
    }

    stage('Unit tests') {
        sh 'make test'
    }

    stage('Prepare artifact') {
        sh 'make install'
    }

    stage('Upload artifacts') {
        sh 'zip -r curl_app_$(date +%Y-%m-%d-%H:%M:%S).zip curl_app'
        def uploadSpec = '''{
             "files": [
                    {
                    "pattern": "curl_app_*.zip",
                    "target": "generic-local/"
                    }
                ]
            }'''
        server.upload spec: uploadSpec, failNoOp: true
        sh 'rm -r curl_app*'
    }

    stage ('Publish build info') {
            server.publishBuildInfo buildInfo
    }
}

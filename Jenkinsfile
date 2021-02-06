def artifactory_name = 'Artifactory'
//def artifactory_repo = 'conan-local'
def repo_url = 'https://github.com/curl/curl.git'
def repo_branch = 'master'

node('Agent') {
    def server = Artifactory.server artifactory_name
    /*def client = Artifactory.newConanClient()
    def serverName = client.remote.add server: server, repo: artifactory_repo*/

    stage('Get project') {
        git branch: repo_branch, url: repo_url
    }

    stage('Build/Test project') {        
        sh "./buildconf && ./configure --prefix=/`pwd`/curl_app && make install"           
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

    }
}

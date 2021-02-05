def artifactory_name = "Artifactory"
def artifactory_repo = "conan-local"
def repo_url = 'https://github.com/curl/curl.git'
def repo_branch = 'master'

node("Agent") {
    def server = Artifactory.server artifactory_name
    def client = Artifactory.newConanClient()
    def serverName = client.remote.add server: server, repo: artifactory_repo

    stage("Get project"){
        git branch: repo_branch, url: repo_url
    }

   
   stage("Build/Test project"){
        dir ('build') {
          sh "cmake ../ && make"
        }
    }
   
   stage("Upload packages"){
        String command = "upload \"*\" --all -r ${serverName} --confirm"
        def b = client.run(command: command)
        server.publishBuildInfo b
    }
}

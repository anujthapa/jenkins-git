
import groovy.json.JsonSlurperClassic 
import groovy.json.JsonOutput 

def checkFirst(){
    def url = "http://localhost:8080/job/jenkins-test1/";
    def data = sh "curl -u anuj:anuj http://localhost:8080/job/jenkins-test1/lastSuccessfulBuild/api/json?path=/*/lastStableBuild/number"
    println("url"+ build_id)
}

node () {
    def config;

    sh "printenv"
     stage("Clean Workspace") {
          if(env.SKIP_CLEAN_WORKSPACE != null && env.SKIP_CLEAN_WORKSPACE == 'false'){
              // clean docker container and images older than a week
              lock(label: "docker-lock-${env.NODE_NAME}") {
                  sh "docker container prune --force --filter 'until=168h'"
                  sh "docker image prune -a --force --filter 'until=168h'"
              }
          }
    }
    def buildResult
    def isImageBuilt = "abc"

    def tag
    def repo
    stage("check") {
        //Use lock shared with CRT to guarantee that no deploy done during CRT
        echo "checking the api"
        checkFirst()   
    }
    stage("Using curl example") {
        script {
                    final String url = "-u anuj:anuj http://localhost:8080/job/jenkins-test1/api/json?pretty"
                    final String response = sh(script: "curl -s $url", returnStdout: true).trim()
                    echo response
                    def json = JsonOutput.toJson(response)
                    newResponse =  response.replaceAll("\n    ","")
                    println("buildnumber"+newResponse+"build number"+ newResponse.lastSuccessfulBuild)
                }
        
    }
    
}

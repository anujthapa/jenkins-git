
import groovy.json.JsonSlurperClassic 
import groovy.json.JsonOutput
import java.io.BufferedReader
import java.io.InputStreamReader
import java.io.OutputStreamWriter
import java.net.URL
import java.net.URLConnection

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
    def pipe_one_response 
    def pipe_two_response
    stage("check") {
        //Use lock shared with CRT to guarantee that no deploy done during CRT
        echo "checking the api"
        checkFirst()   
    }
    stage("Using curl example") {
        script {
                    final String url = "-u anuj:anuj http://localhost:8080/job/jenkins-test1/api/xml?xpath=/*/lastStableBuild/number"
                    final String response = sh(script: "curl -s $url", returnStdout: true).trim()
                    pipe_one_response = response
                    pipe_two_response= build_id
            if (env.dryRun){
            println("dryRun"+dryRun)
            }
                }
        
    }
    stage("run another pipleine"){
        if (pipe_one_response!=pipe_two_response){
                    println("pipe_one and pipe_two"+pipe_one_response+"second"+pipe_two_response)
                    sh '''curl -u anuj:anuj "http://localhost:8080/job/jenkins-demo/buildWithParameters?name=anuj&token=1234"'''
        }
        else{
            println("no need to run other pipeline")
        }
    }
    
}

@Grab(group='com.cdancy', module='jenkins-rest', version='0.0.18')
import groovy.json.JsonSlurperClassic
import com.cdancy.jenkins.rest.JenkinsClient



node (getNode()) {
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
        
        JenkinsClient client = JenkinsClient.builder()
            .endPoint("http://localhost:8080/job/jenkins-demo/") // Optional. Defaults to http://127.0.0.1:8080
            .credentials("anuj:1234") // Optional.
            .build()

        println(client.api().systemApi().systemInfo())    
        
    }
}

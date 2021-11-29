import groovy.json.JsonSlurperClassic


def getNode(defaultNode){
    if (env.JenkinsNode?.trim()){
        return env.JenkinsNode.trim()
    }
    if (env.DEPLOY_SITE?.trim()){
        return env.DEPLOY_SITE.trim() + "&&" + defaultNode
    }
    return "oulu&&" + defaultNode
}

node (getNode("deploy&&${env.DEPLOY_SITE.trim()}")) {
    def sshUrl = env.gitlabTargetRepoSshUrl ?: 'git@gitlabe2.ext.net.nokia.com:Otava/ota-matic-test-runner.git'
    def config;

    if (env.DEPLOY_TYPE == "production" && (env.DEPLOY_IMAGE_TAG == null || env.DEPLOY_IMAGE_TAG.trim() == "")){
      error("For production deployment, you need to set value for param DEPLOY_IMAGE_TAG")
    }

    currentBuild.description = "Deploy to ${env.DEPLOY_CONFIG}"

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

    stage("Clone") {
         clone(env.gitlabBranch, sshUrl)
         def fileText = readFile(file: "backend/configs/testlines/${env.DEPLOY_SITE}/${env.DEPLOY_CONFIG}")
         echo "Config: ${fileText}"
         config = new JsonSlurperClassic().parseText(fileText)
    }
    def buildResult
    def isImageBuilt = env.DEPLOY_TYPE == "development" && env.DEPLOY_FROM_BRANCH != null && env.DEPLOY_FROM_BRANCH.trim()
    if(isImageBuilt){
      stage("Build") {
          buildResult = build job: 'OTAVA/OTA-matic-test-runner-build-dev', parameters: [
            stringParam(name: 'gitlabBranch', value: env.DEPLOY_FROM_BRANCH),
            stringParam(name: 'SKIP_UNIT_TEST', value: env.SKIP_UNIT_TEST),
            stringParam(name: 'SKIP_SMOKE_TEST', value: env.SKIP_SMOKE_TEST),
            stringParam(name: 'SKIP_CI_DEPLOY_TEST', value: 'true'),
            booleanParam(name: 'PUSH_TO_DOCKER_REPO', value: true)
          ]
      }
    } else {
      echo "No image building"
    }

    def tag
    def repo
    stage("Deploy") {
        //Use lock shared with CRT to guarantee that no deploy done during CRT
        echo "Check CRT lock. If CRT running, we remain here waiting in queue..."
        lock(label: env.DEPLOY_CONFIG) {
            sshagent(credentials: [config.otaMatic.deploy.jenkinsSshKeyId]) {
              String prodFlag = ""
              if(env.DEPLOY_TYPE == "production"){
                  tag = env.DEPLOY_IMAGE_TAG
                  prodFlag = "--prod"
              } else {
                 if(isImageBuilt){
                   tag = buildResult.getBuildVariables().get('PUSHED_DOCKER_IMAGE_TAG')
                 } else {
                   tag = env.DEPLOY_IMAGE_TAG
                 }
              }
              currentBuild.description += " ${tag}"
              sh "export PIP_CONFIG_FILE=./pip.conf && pip3 install --user -r ./backend/scripts/requirements.txt"
              sh "./deploy.sh --site ${env.DEPLOY_SITE} --config ${env.DEPLOY_CONFIG} --ci --tag ${tag} ${prodFlag}"
            }
        }
    }
}

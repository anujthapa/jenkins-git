pipeline {
    agent any
    stages {
        stage('check') {
            steps {
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
        stage('run') {
            steps {
                sh 'echo "running Jenkins-demo"'
                def response = httpRequest 'http://localhost:8080/job/jenkins-demo/api/json?pretty=true'
                printlm("status"+response)

                sh ''' curl -X POST -u anuj:anuj http://localhost:8080/job/jenkins-demo/build
                '''
            }
        }
    }
}



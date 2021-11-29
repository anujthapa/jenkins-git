pipeline {
    agent any
    stages {
        stage('check') {
            steps {
                echo "Hello World"
                echo "Multiline shell steps works too"
                sh '''curl "http://localhost:8080/job/jenkins-demo/api/json"'''
             
            }
        }
        stage('run') {
            steps {
                sh 'echo "running Jenkins-demo"'
                sh ''' curl -X POST -u anuj:anuj http://localhost:8080/job/jenkins-demo/build
                '''
            }
        }
    }
}



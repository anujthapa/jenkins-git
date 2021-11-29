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
                sh ''' curl -X POST -u anuj:1234 http://localhost:8080/job/jenkins-demo/build?token=1234
                '''
            }
        }
    }
}



pipeline {
    agent any

    stages {
        stage('Test Git Access') {
            steps {
                script {
                    sh 'git clone https://github.com/OTyshche/Microservice-CI-CD-Pipeline'
                }
            }
        }
    }
}

pipeline {
    agent any

    environment {
        BACKEND_IMAGE = 'my-backend:latest'
        FRONTEND_IMAGE = 'my-frontend:latest'
    }

    triggers {
        githubPush()  // This triggers the pipeline on a GitHub push event.
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/OTyshche/Microservice-CI-CD-Pipeline'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'docker build -t $BACKEND_IMAGE .'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t $FRONTEND_IMAGE .'
                }
            }
        }

         stage('Run Backend Container') {
            steps {
                dir('backend') {
                    // Stop and remove any existing container with the same name
                    sh 'docker stop backend_container || true'
                    sh 'docker rm -f backend_container || true'
                    // Run the backend container
                    sh 'docker run -d --name backend_container -p 3001:3000 my-backend:latest'
                }
            }
        }
        
        stage('Test Backend') {
            steps {
                dir('backend') {
                    // Run test script locally (on Jenkins agent), assuming it sends HTTP requests to localhost:5000
                    sh 'python3 test_backend.py'
                }
            }
        }
        
        stage('Stop Backend Container') {
            steps {
                // Stop the backend container after tests
                sh 'docker rm -f backend_container || true'
            }
        }
        
        stage('Deploy Locally') {
            steps {
                sh '''
                docker network create my-network || true
                docker stop backend || true
                docker rm -f backend || true
                docker run -d --name backend --network my-network -p 3000:3000 $BACKEND_IMAGE
                docker stop frontend || true
                docker rm -f frontend || true
                docker run -d --name frontend --network my-network -p 8000:80 $FRONTEND_IMAGE
                '''
            }
        }
    }

    post {
        success {
            echo 'Container successfuly deployed'
            // sh 'docker stop frontend || true'
            // sh 'docker stop backend || true'
        }
        failure {
            echo 'Tests failed. Cleaning up containers...'
            sh 'docker stop frontend || true'
            sh 'docker stop backend || true'
            sh 'docker rm -f frontend || true'
            sh 'docker rm -f backend || true'
            sh 'docker stop backend_container || true'
            sh 'docker rm -f backend_container || true'
        }
    }
}

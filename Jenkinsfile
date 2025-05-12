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
                git 'https://github.com/OTyshche/Microservice-CI-CD-Pipeline'
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
                    // Start the backend container in the background
                    sh 'docker run -d --name backend_container -p 5000:5000 $BACKEND_IMAGE'
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
                sh 'docker stop backend_container || true'
            }
        }
        
        stage('Deploy Locally') {
            steps {
                sh '''
                docker network create my-network || true
                docker run -d --name backend $BACKEND_IMAGE
                docker run -d --name frontend -p 8080:80 $FRONTEND_IMAGE
                '''
            }
        }
    }

    post {
        always {
            echo 'Container successfuly deployed'
            // sh 'docker stop frontend || true'
            // sh 'docker stop backend || true'
        }
    }
}

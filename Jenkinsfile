pipeline {
    agent none
    stages {
        stage('Checkout') {
            agent any
            steps {
                // Run checkout on a normal Jenkins agent which has `git` installed
                git branch: 'main', url: 'https://github.com/L-Passakorn/jenkins-demo-app.git'
            }
        }
        stage('Build Image') {
            agent {
                docker {
                    image 'docker:20.10.7'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'docker build -t jenkins-demo-app:latest .'
            }
        }
        stage('Run Container') {
            agent {
                docker {
                    image 'docker:20.10.7'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'docker run -d -p 5000:5000 --name demo-app jenkins-demo-app:latest'
            }
        }
    }
}

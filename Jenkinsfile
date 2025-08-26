pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Run checkout on a normal Jenkins agent which has `git` installed
                git branch: 'main', url: 'https://github.com/L-Passakorn/jenkins-demo-app.git'
            }
        }
        stage('Build Image') {
            steps {
                // Requires the agent/node to have Docker installed and access to the docker daemon
                sh 'docker build -t jenkins-demo-app:latest .'
            }
        }
        stage('Run Container') {
            steps {
                sh 'docker run -d -p 5000:5000 --name demo-app jenkins-demo-app:latest'
            }
        }
    }
}

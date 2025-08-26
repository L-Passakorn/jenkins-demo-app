pipeline {
    agent any
    stages {
                stage('Diagnose Docker') {
                        steps {
                                // Print useful diagnostic info so logs show socket perms and user
                                sh '''
                                echo "=== /var/run/docker.sock ==="
                                ls -l /var/run/docker.sock || true
                                echo "=== id ==="
                                id || true
                                echo "=== groups ==="
                                groups || true
                                echo "=== docker version (may fail) ==="
                                docker version || true
                                '''
                        }
                }
        stage('Checkout') {
            steps {
                // Run checkout on a normal Jenkins agent which has `git` installed
                git branch: 'main', url: 'https://github.com/L-Passakorn/jenkins-demo-app.git'
            }
        }
        stage('Build Image') {
            steps {
                                // Try a normal docker build; if it fails try sudo as a fallback and print socket info on failure
                                                sh '''
                                                # Prefer bash (for pipefail) when available, otherwise fall back to POSIX sh
                                                if command -v bash >/dev/null 2>&1; then
                                                    pipeline {
                                                            agent any
                                                            stages {
                                                                    stage('Diagnose Docker') {
                                                                            steps {
                                                                                    // Print useful diagnostic info so logs show socket perms and user
                                                                                    sh '''
                                                                                    echo "=== /var/run/docker.sock ==="
                                                                                    ls -l /var/run/docker.sock || true
                                                                                    echo "=== id ==="
                                                                                    id || true
                                                                                    echo "=== groups ==="
                                                                                    groups || true
                                                                                    echo "=== docker version (may fail) ==="
                                                                                    docker version || true
                                                                                    '''
                                                                            }
                                                                    }

                                                                    stage('Checkout') {
                                                                            steps {
                                                                                    // Run checkout on a normal Jenkins agent which has `git` installed
                                                                                    git branch: 'main', url: 'https://github.com/L-Passakorn/jenkins-demo-app.git'
                                                                            }
                                                                    }

                                                                    stage('Build Image') {
                                                                            steps {
                                                                                    // Try a normal docker build; if it fails try sudo as a fallback and print socket info on failure
                                                                                    sh '''
                                                                                    # Prefer bash (for pipefail) when available, otherwise fall back to POSIX sh
                                                                                    if command -v bash >/dev/null 2>&1; then
                                                                                        exec bash -lc '
                                                                                            set -euo pipefail
                                                                                            echo "Attempting docker build..."
                                                                                            if docker build -t jenkins-demo-app:latest .; then
                                                                                                echo "docker build succeeded"
                                                                                            elif command -v sudo >/dev/null 2>&1 && sudo docker build -t jenkins-demo-app:latest .; then
                                                                                                echo "sudo docker build succeeded"
                                                                                            else
                                                                                                echo "docker build failed; showing socket details"
                                                                                                ls -l /var/run/docker.sock || true
                                                                                                stat /var/run/docker.sock || true
                                                                                                echo "If permissions are denied, add the Jenkins user to the docker group on the agent host: \"sudo usermod -aG docker jenkins\" and restart the agent process (or reboot)."
                                                                                                echo "If this is a containerized agent, ensure /var/run/docker.sock is mounted into the container and the container user has the host docker GID or run the agent as root."
                                                                                                exit 1
                                                                                            fi'
                                                                                    else
                                                                                        # POSIX fallback: do not use pipefail (not available in /bin/sh on some images)
                                                                                        set -eu
                                                                                        echo "Attempting docker build..."
                                                                                        if docker build -t jenkins-demo-app:latest .; then
                                                                                            echo "docker build succeeded"
                                                                                        elif command -v sudo >/dev/null 2>&1 && sudo docker build -t jenkins-demo-app:latest .; then
                                                                                            echo "sudo docker build succeeded"
                                                                                        else
                                                                                            echo "docker build failed; showing socket details"
                                                                                            ls -l /var/run/docker.sock || true
                                                                                            stat /var/run/docker.sock || true
                                                                                            echo "If permissions are denied, add the Jenkins user to the docker group on the agent host: \"sudo usermod -aG docker jenkins\" and restart the agent process (or reboot)."
                                                                                            echo "If this is a containerized agent, ensure /var/run/docker.sock is mounted into the container and the container user has the host docker GID or run the agent as root."
                                                                                            exit 1
                                                                                        fi
                                                                                    fi
                                                                                    '''
                                                                            }
                                                                    }

                                                                    stage('Run Container') {
                                                                            steps {
                                                                                    sh 'docker run -d -p 5000:5000 --name demo-app jenkins-demo-app:latest'
                                                                            }
                                                                    }
                                                            }
                                                    }

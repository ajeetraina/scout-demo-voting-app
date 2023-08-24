pipeline {
    agent any
    
    environment {
        DOCKER_HUB_USER = credentials('DOCKER_HUB_USER ')
        DOCKER_HUB_PAT = credentials('DOCKER_HUB_PAT')
    }
    
    stages {
        stage('Build and Analyze Vote Service') {
            steps {
                script {
                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'

                    // Log into Docker Hub
                    sh "echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin"

                    // Build vote Docker Image
                    sh "docker build --pull -t $DOCKER_HUB_USER/vote:$GIT_COMMIT ./vote"
                    sh "docker push $DOCKER_HUB_USER/vote:$GIT_COMMIT"

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves $DOCKER_HUB_USER/vote:$GIT_COMMIT --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Analyze Worker Service') {
            steps {
                script {
                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'

                    // Log into Docker Hub
                    sh "echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin"

                    // Build worker Docker Image
                    sh "docker build --pull -t $DOCKER_HUB_USER/worker:$GIT_COMMIT ./worker"
                    sh "docker push $DOCKER_HUB_USER/worker:$GIT_COMMIT"

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves $DOCKER_HUB_USER/worker:$GIT_COMMIT --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Analyze Result Service') {
            steps {
                script {
                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'

                    // Log into Docker Hub
                    sh "echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin"

                    // Build result Docker Image
                    sh "docker build --pull -t $DOCKER_HUB_USER/result:$GIT_COMMIT ./result"
                    sh "docker push $DOCKER_HUB_USER/result:$GIT_COMMIT"

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves $DOCKER_HUB_USER/result:$GIT_COMMIT --exit-code --only-severity critical,high"
                }
            }
        }
    }
}

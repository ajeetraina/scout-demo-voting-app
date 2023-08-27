pipeline {
    agent {
        docker {
            image 'jenkinsci/blueocean' // Use a Docker image that has Docker and Jenkins tools
            args "--group-add docker"
        }
    }

    environment {
        DOCKER_HUB_USER = credentials('DOCKER_HUB_USER')
        DOCKER_HUB_PAT = credentials('DOCKER_HUB_PAT')
    }
    
    stages {
        stage('Build and Analyze Vote Service') {
            steps {
                script {
                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b $WORKSPACE'

                    // Log into Docker Hub
                    withCredentials([string(credentialsId: 'DOCKER_HUB_PAT', variable: 'DOCKER_HUB_PAT'),
                                     string(credentialsId: 'DOCKER_HUB_USER', variable: 'DOCKER_HUB_USER')]) {
                        sh """
                        echo \$DOCKER_HUB_PAT | docker login -u \$DOCKER_HUB_USER --password-stdin
                        """
                    }

                    // Build and push vote Docker Image
                    sh """
                    docker build --pull -t \$DOCKER_HUB_USER/vote:\$GIT_COMMIT ./vote
                    docker push \$DOCKER_HUB_USER/vote:\$GIT_COMMIT
                    """

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves \$DOCKER_HUB_USER/vote:\$GIT_COMMIT --exit-code --only-severity critical,high"
                }
            }
        }
    }
}

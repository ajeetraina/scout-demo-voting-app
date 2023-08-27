pipeline {
    agent {
        docker {
            image 'docker:20' // Use a Docker image that has Docker installed
            args '--group-add $(stat -c %g /var/run/docker.sock)' // Grants access to Docker socket
        }
    }

    environment {
        DOCKER_HUB_USER = credentials('DOCKER_HUB_USER')
        DOCKER_HUB_PAT = credentials('DOCKER_HUB_PAT')
    }

    stages {
        stage('Build and Analyze Services') {
            steps {
                script {
                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b $WORKSPACE'

                    // Log into Docker Hub using Username with Password credentials
                    withCredentials([
                        usernamePassword(credentialsId: 'DOCKER_HUB_USER', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD'),
                        string(credentialsId: 'DOCKER_HUB_PAT', variable: 'DOCKER_HUB_PAT')
                    ]) {
                        sh """
                        echo \$DOCKER_HUB_PASSWORD | docker login -u \$DOCKER_HUB_USERNAME --password-stdin
                        """
                    }

                    // Define Docker image names for each service
                    def serviceImageMap = [
                        'vote': "\$DOCKER_HUB_USERNAME/vote:\$GIT_COMMIT",
                        'worker': "\$DOCKER_HUB_USERNAME/worker:\$GIT_COMMIT",
                        'result': "\$DOCKER_HUB_USERNAME/result:\$GIT_COMMIT"
                    ]

                    // Build, push, and analyze Docker images for each service
                    serviceImageMap.each { serviceName, imageName ->
                        sh "echo 'Building $serviceName service'"
                        sh "docker build --pull -t $imageName ./$serviceName"
                        sh "docker push $imageName"
                        sh "echo 'Analyzing $serviceName service for vulnerabilities'"
                        sh "docker-scout cves $imageName --exit-code --only-severity critical,high"
                    }
                }
            }
        }
    }
}

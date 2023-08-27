pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = credentials('DOCKER_HUB_USER')
        DOCKER_HUB_PAT = credentials('DOCKER_HUB_PAT')
        DOCKER_BIN = '/usr/bin/docker' // Replace with the actual path to the Docker binary on your system
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
                        echo \$DOCKER_HUB_PASSWORD | ${DOCKER_BIN} login -u \$DOCKER_HUB_USERNAME --password-stdin
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
                        sh "${DOCKER_BIN} build --pull -t $imageName ./$serviceName"
                        sh "${DOCKER_BIN} push $imageName"
                        sh "echo 'Analyzing $serviceName service for vulnerabilities'"
                        sh "docker-scout cves $imageName --exit-code --only-severity critical,high"
                    }
                }
            }
        }
    }
}

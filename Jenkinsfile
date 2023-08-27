pipeline {
    agent {
        docker {
            image 'jenkinsci/blueocean' // Use a Docker image that has Docker installer
            args "--group-add docker"
        }
    }

    environment {
        DOCKER_HUB_USER = "ajeetraina"
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
                        echo \$DOCKER_HUB_PAT | ${dockerBin} login -u \$DOCKER_HUB_USER --password-stdin
                        """
                    }

                    // Build and push vote Docker Image
                    sh """
                    ${dockerBin} build --pull -t \$DOCKER_HUB_USER/vote:\$GIT_COMMIT ./vote
                    ${dockerBin} push \$DOCKER_HUB_USER/vote:\$GIT_COMMIT
                    """

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves \$DOCKER_HUB_USER/vote:\$GIT_COMMIT --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Analyze Worker Service') {
            steps {
                script {
                    def dockerPath = tool name: 'Docker', type: 'Tool'
                    def dockerBin = "${dockerPath}/bin/docker"

                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b $WORKSPACE'

                    // Log into Docker Hub
                    withCredentials([string(credentialsId: 'DOCKER_HUB_PAT', variable: 'DOCKER_HUB_PAT'),
                                     string(credentialsId: 'DOCKER_HUB_USER', variable: 'DOCKER_HUB_USER')]) {
                        sh """
                        echo \$DOCKER_HUB_PAT | ${dockerBin} login -u \$DOCKER_HUB_USER --password-stdin
                        """
                    }

                    // Build and push worker Docker Image
                    sh """
                    ${dockerBin} build --pull -t \$DOCKER_HUB_USER/worker:\$GIT_COMMIT ./worker
                    ${dockerBin} push \$DOCKER_HUB_USER/worker:\$GIT_COMMIT
                    """

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves \$DOCKER_HUB_USER/worker:\$GIT_COMMIT --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Analyze Result Service') {
            steps {
                script {
                    def dockerPath = tool name: 'Docker', type: 'Tool'
                    def dockerBin = "${dockerPath}/bin/docker"

                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b $WORKSPACE'

                    // Log into Docker Hub
                    withCredentials([string(credentialsId: 'DOCKER_HUB_PAT', variable: 'DOCKER_HUB_PAT'),
                                     string(credentialsId: 'DOCKER_HUB_USER', variable: 'DOCKER_HUB_USER')]) {
                        sh """
                        echo \$DOCKER_HUB_PAT | ${dockerBin} login -u \$DOCKER_HUB_USER --password-stdin
                        """
                    }

                    // Build and push result Docker Image
                    sh """
                    ${dockerBin} build --pull -t \$DOCKER_HUB_USER/result:\$GIT_COMMIT ./result
                    ${dockerBin} push \$DOCKER_HUB_USER/result:\$GIT_COMMIT
                    """

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves \$DOCKER_HUB_USER/result:\$GIT_COMMIT --exit-code --only-severity critical,high"
                }
            }
        }
    }
}

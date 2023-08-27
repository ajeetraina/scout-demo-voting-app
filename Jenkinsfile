pipeline {
    agent any

    environment {
        IMAGE_TAG_VOTE = 'ajeetraina/scout-demo-voting-app-vote'
        IMAGE_TAG_RESULT = 'ajeetraina/scout-demo-voting-app-result'
        IMAGE_TAG_WORKER = 'ajeetraina/scout-demo-voting-app-worker'
    }

    stages {
        stage('Build and Scout Vote Service') {
            steps {
                script {
                    checkout scm

                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'
                    echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin

                    // Build and tag Docker image for vote service
                    sh "docker build -t $IMAGE_TAG_VOTE ./vote"

                    // Analyze image for CVEs
                    sh "docker-scout cves $IMAGE_TAG_VOTE --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Scout Result Service') {
            steps {
                script {
                    checkout scm

                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'

                    // Build and tag Docker image for result service
                    sh "docker build -t $IMAGE_TAG_RESULT ./result"

                    // Analyze image for CVEs
                    sh "docker-scout cves $IMAGE_TAG_RESULT --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Scout Worker Service') {
            steps {
                script {
                    checkout scm

                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'

                    // Build and tag Docker image for worker service
                    sh "docker build -t $IMAGE_TAG_WORKER ./worker"

                    // Analyze image for CVEs
                    sh "docker-scout cves $IMAGE_TAG_WORKER --exit-code --only-severity critical,high"
                }
            }
        }
    }
    
    post {
        failure {
            script {
                currentBuild.result = 'FAILURE'
            }
        }
    }
}

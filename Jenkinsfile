pipeline {
agent any

environment {
    IMAGE_TAG_VOTE = 'ajeetraina/scout-demo-voting-app-vote'
    IMAGE_TAG_RESULT = 'ajeetraina/scout-demo-voting-app-result'
    IMAGE_TAG_WORKER = 'ajeetraina/scout-demo-voting-app-worker'
    DOCKER_HUB_PAT = credentials('docker-hub-pat')
    DOCKER_HUB_USER = 'ajeetraina'
}

stages {
    stage('Build and Scout Vote Service') {
        steps {
            script {
                checkout scm

                // Install Docker Scout
                sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b ~/bin'

                // Log into Docker Hub
               sh 'echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin'

                // Analyze image for CVEs
                sh "docker-scout cves ${IMAGE_TAG_VOTE} --exit-code --only-severity critical,high"

                // Get recommendations for remediation steps
                sh "docker-scout recommendations ${IMAGE_TAG_VOTE}"

            }
        }
    }

    stage('Build and Scout Result Service') {
        steps {
            script {
                checkout scm

                // Install Docker Scout
                sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b ~/bin'

                // Log into Docker Hub
               sh 'echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin'

                // Analyze image for CVEs
                sh "docker-scout cves ${IMAGE_TAG_RESULT} --exit-code --only-severity critical,high"

                // Get recommendations for remediation steps
                sh "docker-scout recommendations ${IMAGE_TAG_RESULT}"
            }
        }
    }

    stage('Build and Scout Worker Service') {
        steps {
            script {
                checkout scm

                // Install Docker Scout
                sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b ~/bin'

                // Log into Docker Hub
               sh 'echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin'

                // Analyze image for CVEs
                sh "docker-scout cves ${IMAGE_TAG_WORKER} --exit-code --only-severity critical,high"

                // Get recommendations for remediation steps
                sh "docker-scout recommendations ${IMAGE_TAG_WORKER}"
            }
        }
    }
}
}


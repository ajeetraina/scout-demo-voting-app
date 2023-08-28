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
                withCredentials([string(credentialsId: 'DOCKER_HUB_PAT', variable: 'DOCKER_HUB_PAT'),
                                string(credentialsId: 'DOCKER_HUB_USER', variable: 'DOCKER_HUB_USER')]) {
                                sh """
                              echo \$DOCKER_HUB_PAT | ${dockerBin} login -u \$DOCKER_HUB_USER --password-stdin
                              """

                // Build and tag Docker image for vote service
                sh "docker build -t ${IMAGE_TAG_VOTE} ./vote"

                // Analyze image for CVEs
                sh "docker-scout cves ${IMAGE_TAG_VOTE} --exit-code --only-severity critical,high"

                // Get recommendations for remediation steps
                sh "docker-scout recommendations ${IMAGE_TAG_VOTE}"

                // Publish the results of the vulnerability scan to Slack
                slackSend channel="#vulnerability-scans" message="The vulnerability scan for ${IMAGE_TAG_VOTE} found the following CVEs: ${CVEs}"
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
                sh "docker build -t ${IMAGE_TAG_RESULT} ./result"

                // Analyze image for CVEs
                sh "docker-scout cves ${IMAGE_TAG_RESULT} --exit-code --only-severity critical,high"

                // Get recommendations for remediation steps
                sh "docker-scout recommendations ${IMAGE_TAG_RESULT}"

                // Publish the results of the vulnerability scan to Slack
                slackSend channel="#vulnerability-scans" message="The vulnerability scan for ${IMAGE_TAG_RESULT} found the following CVEs: ${CVEs}"
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
                sh "docker build -t ${IMAGE_TAG_WORKER} ./worker"

                // Analyze image for CVEs
                sh "docker-scout cves ${IMAGE_TAG_WORKER} --exit-code --only-severity critical,high"

                // Get recommendations for remediation steps
                sh "docker-scout recommendations ${IMAGE_TAG_WORKER}"

                // Publish the results of the vulnerability scan to Slack
                slackSend channel="#vulnerability-scans" message="The vulnerability scan for ${IMAGE_TAG_WORKER} found the following CVEs: ${CVEs}"
            }
        }
    }
}
}


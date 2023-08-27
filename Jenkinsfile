pipeline {
    agent {
        docker {
            image 'jenkinsci/blueocean'
            args "--group-add docker --entrypoint ''" // Disable entrypoint
        }
    }

    environment {
        IMAGE_TAG = 'your-image-tag:latest' // Replace with your image tag
    }

    stages {
        stage('Analyze image') {
            steps {
                script {
                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b $WORKSPACE'

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves $IMAGE_TAG --exit-code --only-severity critical,high"
                }
            }
        }
    }
}

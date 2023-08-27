pipeline {
    agent {
        docker {
            image 'jenkinsci/blueocean'
            args "--group-add docker --entrypoint ''" // Disable entrypoint
        }
    }

    environment {
        IMAGE_TAG = 'ajeetraina/scout-demo-voting-app-vote' // Replace with your image tag
    }

    stages {
        stage('Analyze image') {
            steps {
                script {
                    // Install Docker Scout
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'

                    // Analyze and fail on critical or high vulnerabilities
                    sh "docker-scout cves $IMAGE_TAG --exit-code --only-severity critical,high"
                }
            }
        }
    }
}

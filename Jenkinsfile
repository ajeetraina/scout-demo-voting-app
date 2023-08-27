pipeline {
    agent any

    environment {
        IMAGE_TAG_VOTE = 'ajeetraina/scout-demo-voting-app-vote'
        IMAGE_TAG_RESULT = 'ajeetraina/scout-demo-voting-app-result'
        IMAGE_TAG_WORKER = 'ajeetraina/scout-demo-voting-app-worker'
    }

    stages {
        stage('Build and Scout Vote Service') {
            agent {
                docker {
                    image 'cimg/base:stable'
                    args '--group-add docker'
                }
            }
            steps {
                script {
                    checkout()
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'
                    sh "echo \$DOCKER_HUB_PAT | docker login -u \$DOCKER_HUB_USER --password-stdin"
                    sh "docker build -t \$IMAGE_TAG_VOTE ./vote"
                    sh "docker-scout cves \$IMAGE_TAG_VOTE --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Scout Result Service') {
            agent {
                docker {
                    image 'cimg/base:stable'
                    args '--group-add docker'
                }
            }
            steps {
                script {
                    checkout()
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'
                    sh "echo \$DOCKER_HUB_PAT | docker login -u \$DOCKER_HUB_USER --password-stdin"
                    sh "docker build -t \$IMAGE_TAG_RESULT ./result"
                    sh "docker-scout cves \$IMAGE_TAG_RESULT --exit-code --only-severity critical,high"
                }
            }
        }

        stage('Build and Scout Worker Service') {
            agent {
                docker {
                    image 'cimg/base:stable'
                    args '--group-add docker'
                }
            }
            steps {
                script {
                    checkout()
                    sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'
                    sh "echo \$DOCKER_HUB_PAT | docker login -u \$DOCKER_HUB_USER --password-stdin"
                    sh "docker build -t \$IMAGE_TAG_WORKER ./worker"
                    sh "docker-scout cves \$IMAGE_TAG_WORKER --exit-code --only-severity critical,high"
                }
            }
        }
    }
}

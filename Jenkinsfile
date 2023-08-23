pipeline {
    agent any
    
    environment {
        DOCKER_HUB_USER = credentials('DOCKER_HUB_USER')
        DOCKER_HUB_PAT = credentials('DOCKER_HUB_PAT')
    }

    stages {
        stage('Build and Analyze Vote Service') {
            steps {
                script {
                    buildAndAnalyze('vote', 'vote/Dockerfile')
                }
            }
        }

        stage('Build and Analyze Result Service') {
            steps {
                script {
                    buildAndAnalyze('result', 'result/Dockerfile')
                }
            }
        }

        stage('Build and Analyze Worker Service') {
            steps {
                script {
                    buildAndAnalyze('worker', 'worker/Dockerfile')
                }
            }
        }
    }
}

def buildAndAnalyze(serviceName, dockerfilePath) {
    def imageTag = "ajeetraina/scout-demo-voting-app-${serviceName}"
    def scoutCommand = "docker-scouts cves ${imageTag} --exit-code --only-severity critical,high"

    stage("Build ${serviceName} Docker Image") {
        steps {
            sh "docker build -t ${imageTag} -f ${dockerfilePath} ."
        }
    }

    stage("Analyze ${serviceName} for CVEs") {
        steps {
            sh "docker pull ${imageTag}" // Pull the built image

            // Log into Docker Hub
            sh "echo ${DOCKER_HUB_PAT} | docker login -u ${DOCKER_HUB_USER} --password-stdin"

            sh scoutCommand
        }
    }
}


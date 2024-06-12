def frontendImage = "https://github.com/1wonderwonder/frontend"
def backendImage = "https://github.com/1wonderwonder/backend"

pipeline {
    agent {
        label 'agent'
    }
    parameters {
        string(defaultValue: 'latest', name: 'backendDockerTag')
        string(defaultValue: 'latest', name: 'frontendDockerTag')
    }

    stages {
        stage('Get Code') {
            steps {
                checkout scm
            }
        }

        stage('Adjust version') {
            steps {
                script {
                    currentBuild.description = params.backendDockerTag
                }
            }
        }
        stage('Delete previous containers') {
            steps {
                sh '''docker rm -f backend
                docker rm -f backend'''
            }
        }
        stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                            sh "docker-compose up -d"
                    }
                }
            }
        }
    }
}
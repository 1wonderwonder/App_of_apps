def frontendImage = "wonderwow/frontend"
def backendImage = "wonderwow/backend"

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
                docker rm -f frontend'''
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
        stage('Selenium tests') {
            environment {
               PIP_BREAK_SYSTEM_PACKAGES = "1"
            }
            steps {
                sh "pip3 install -r requirements.txt"
                sh "python3 -m pytest ./test/selenium/frontendTest.py"
            }
        }
    }

    post {
      always {
            sh "docker-compose down"
            cleanWs()
        }
    }
}
    
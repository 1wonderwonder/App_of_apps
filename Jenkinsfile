def frontendImage = "wonderwow/frontend"
def backendImage = "wonderwow/backend"

pipeline {
    agent {
        label 'agent'
    }
    tools {
        terraform 'Terraform'
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
                sh "pip3 install -r test/selenium/requirements.txt"
                sh "python3 -m pytest test/selenium/frontendTest.py"
            }
        }
        stage('Run terraform') {
            steps {
                dir('Terraform') { 
                git branch: 'main', url: 'https://github.com/1wonderwonder/Terraform'
                    withAWS(credentials:'AWS', region: 'us-east-1') {
                        sh 'terraform init -backend-config=bucket=wiktor-zabinski-devops-core-18'
                        sh 'terraform apply -auto-approve -var bucket_name=wiktor-zabinski-devops-core-18'
                    } 
                }
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
    
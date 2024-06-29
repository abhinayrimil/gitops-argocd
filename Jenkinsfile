pipeline{
    agent any
    environment{
        DOCKERHUB_USERNAME = "abhinayrimil"
        APP_NAME = "gitops-argocd"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "${APP_NAME}"
        REGISTRY_CREDS = "dockerhub"  
    }

    stages{
        stage('Cleanup workspace'){ 
            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage('Checkout from SCM'){
            steps{
                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/abhinayrimil/gitops-argocd.git',
                    branch: 'main'
                }
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    docker_image = docker.build("${IMAGE_NAME}")
                }
            }
        }
    }
}
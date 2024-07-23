pipeline{
    agent any
    environment{
        DOCKERHUB_USERNAME = "abhinayrimil"
        APP_NAME = "gitops-argocd"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}/${APP_NAME}:${IMAGE_TAG}"
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
        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('https://index.docker.io/v1/',REGISTRY_CREDS){
                        docker_image.push()
                    }
                }
            }
        }
        stage('Delete Docker images'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}"
                }
            }
        }
        stage('Updating K8 deployment file'){
            steps{
                script{
                    sh """
                    cat deployment.yaml
                    sed -i 's|${APP_NAME}.*|${IMAGE_NAME}|g' deployment.yaml
                    cat deployment.yaml
                    """
                }
            }
        }
        stage('Push the modified deployment file to Git'){
            steps{
                script{
                    sh """
                    git config --global user.name "abhinayrimil"
                    git config --global user.email "abhinayrimil@gmail.com"
                    git add deployment.yaml
                    git commit -m "Updating deployment with image tag"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/abhinayrimil/gitops-argocd.git main"
                    }

                    
                }
            }
        }
    }
}
pipeline {
    agent { 
        label "Jenkins-Slave" 
    }

    environment {
        APP_NAME    = "nextgen-app-pipeline"
        IMAGE_TAG   = "1.0.0-${BUILD_NUMBER}" // Or override this with a parameter
        IMAGE_NAME  = "ginger2000/nextgen-app" // Define if your image is hosted on DockerHub
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("SCM Checkout") {
            steps {
                git branch: 'main', 
                    credentialsId: 'GitHub-Token', 
                    url: 'https://github.com/yomesky2000/gitops-register-app'
                echo "Continuous Deployment GitOps Repo Cloned Successfully"
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                script {
                    def newTag = "${IMAGE_NAME}:${IMAGE_TAG}"
                    echo "Updating deployment.yaml with image: ${newTag}"
                }

                sh '''
                    echo "Before update:"
                    cat deployment.yaml

                    # Update line that contains the image for APP_NAME
                    perl -pe "s|${APP_NAME}.*|${APP_NAME}:${IMAGE_TAG}|g" deployment.yaml > tmp.yaml
                    mv tmp.yaml deployment.yaml
                    #sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml

                    echo "After update:"
                    cat deployment.yaml
                '''
                echo "Container tag updated successfully"
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "Ginger"
                    git config --global user.email "ginger0@gmail.com"
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest with new tag ${IMAGE_TAG}" || echo "No changes to commit"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/yomesky2000/gitops-register-app main"
                    echo "Pushed updated changes to GitOps repo"
                }
            }
        }
    }
}

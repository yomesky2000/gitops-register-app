pipeline {
    agent { 
        label "Jenkins-Slave" 
    }

    environment {
        APP_NAME = "register-app-pipeline"
        IMAGE_TAG = "v${BUILD_NUMBER}"
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
                sh """
                    echo "Using APP_NAME: ${APP_NAME}"
                    echo "Using IMAGE_TAG: ${IMAGE_TAG}"

                    echo "Before updating image tag:"
                    cat deployment.yaml

                    sed -i "s|${APP_NAME}:.*|${APP_NAME}:${IMAGE_TAG}|g" deployment.yaml

                    echo "After updating image tag:"
                    cat deployment.yaml
                """
                echo "Container tags updated successfully"
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "Ginger"
                    git config --global user.email "ginger0@gmail.com"
                    git add deployment.yaml || true

                    if ! git diff --cached --quiet; then
                        git commit -m "Updated Deployment Manifest to ${IMAGE_TAG}"
                        git push https://github.com/yomesky2000/gitops-register-app main
                        echo "Pushed updated deployment.yaml to GitHub"
                    else
                        echo "No changes to commit."
                    fi
                """
            }
        }
    }
}

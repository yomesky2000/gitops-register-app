pipeline {
    agent { 
        label "Jenkins-Slave" 
    }
    environment {
        APP_NAME = "register-app-pipeline"
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
                    echo "Validate existing docker image version"
                    cat deployment.yaml
                    sed -i "s|^\(\s*image:\s*\).*|\\1${APP_NAME}:${IMAGE_TAG}|g" deployment.yaml
                    echo "Validate newly updated docker image version after modifying deployment.yaml file"
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
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/yomesky2000/gitops-register-app main"
                    echo "Pushing updated changes to Git Repo"
                }
            }
        }
    }
}

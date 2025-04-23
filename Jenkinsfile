pipeline {
    agent {
        label "Jenkins-Slave"
    }

    environment {
        APP_NAME = "register-app-pipeline"
        IMAGE_TAG = "1.0.0-${BUILD_NUMBER}"
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
                script {
                    sh """
                        git config --global user.name "Ginger"
                        git config --global user.email "ginger0@gmail.com"
                        git add deployment.yaml || true
                    """

                    // Only commit and push if there are changes
                    def hasChanges = sh(
                        script: 'git diff --cached --quiet || echo "changed"',
                        returnStdout: true
                    ).trim()

                    if (hasChanges == "changed") {
                        sh "git commit -m 'Updated Deployment Manifest to ${IMAGE_TAG}'"

                        withCredentials([usernamePassword(
                            credentialsId: 'GitHub-Token',
                            usernameVariable: 'GIT_USERNAME',
                            passwordVariable: 'GIT_PASSWORD'
                        )]) {
                            sh """
                                git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/yomesky2000/gitops-register-app main
                            """
                            echo "Pushed updated deployment.yaml to GitHub"
                        }
                    } else {
                        echo "No changes to commit."
                    }
                }
            }
        }
    }
}

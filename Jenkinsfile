pipeline {
    agent {
        kubernetes {
            inheritFrom "kube-agent"
        }
    }
    environment {
        APP_NAME = "cd-pipeline"
        KUBE_CONFIG = "/home/k5master/.kube/config" 
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Nikhil007nsg/cd-pipeline'
            }
        }
        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }
        stage("Push the Changed Deployment File to Git") {
            steps {
                sh """
                   git config --global user.name "nikhil007nsg"
                   git config --global user.email "nikhil007nsg@gmail.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                  # git push origin main
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/Nikhil007nsg/cd-pipeline main"
                }
            }
        }
        stage("Deploy to Kubernetes") {
            steps {
                script {
                    // Apply both deployment and service files using kubectl with the specified kubeconfig
                    sh """
                       kubectl --kubeconfig=${KUBE_CONFIG} apply -f deployment.yaml
                       kubectl --kubeconfig=${KUBE_CONFIG} apply -f service.yaml
                    """
                }
            }
        }
    }
}

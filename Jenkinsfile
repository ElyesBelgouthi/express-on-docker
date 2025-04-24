pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'elyesbelgouthi/express-demo-app'
        HELM_RELEASE_NAME = 'express-demo'
        HELM_CHART_PATH = './helm/express-demo-app'
        HELM_BIN_DIR = '/var/jenkins_home/bin'
    }
    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'helm', url: 'https://github.com/ElyesBelgouthi/express-on-docker.git'
            }
        }
        stage('Construire l\'image Docker') {
            steps {
                script {
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }
        stage('Pousser l\'image Docker') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub_jenkins', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}'
                        sh 'docker push ${DOCKER_IMAGE}'
                    }
                }
            }
        }
        
        stage('Déployer avec Helm') {
            steps {
                script {
                    kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCakNDQWU2Z0F3SUJBZ0lCQVRBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwdGFXNXAKYTNWaVpVTkJNQjRYRFRJMU1EUXlNVEUzTWpJMU0xb1hEVE0xTURReU1ERTNNakkxTTFvd0ZURVRNQkVHQTFVRQpBeE1LYldsdWFXdDFZbVZEUVRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTHZqCi9mZkxJY2xldU5xYWlXSERmcWJCWjJUYnNKTVVpd3g5NWljcG9IMzZ2b1MwaTRSV2JzVUptMkprZ3hRVExxM3oKcWFuZTFCVEJKNEJleUtNcEFZUGg0QlJaVkJaY3dwVUoxclJHemtiU0pFcmxLUDFxRmJ3bW9LVmVpUEtPQzIyTwpVSEw1L3lxbTV0SzZ6YnRoOXdLQVppOVhRQ2VSZGRBTUJpVldXQ21HT1BUSVFDaUowYWJyYll0ZXgyYlR4SmFRCkk4WXk0L0lTZ2hJakFYdExmOEE0RGczelBwdUdNblV3bDQzZ1pMdnlESklrVTBwQ2xuT3NDTm5aZmRaMXBrSy8KbWp5NUZnSFdrYVdtMEF1NmJkc3Fnald3ZzZ2ZkE1NDdOVGpwbW83MUxGSUttRHlDVUpRSlE1NWtiUUFYbFBHeQpMUTZkTC9SekNCbDhiQjJhdXhrQ0F3RUFBYU5oTUY4d0RnWURWUjBQQVFIL0JBUURBZ0trTUIwR0ExVWRKUVFXCk1CUUdDQ3NHQVFVRkJ3TUNCZ2dyQmdFRkJRY0RBVEFQQmdOVkhSTUJBZjhFQlRBREFRSC9NQjBHQTFVZERnUVcKQkJUYU1EaEM4SHVzS0JFQUYvcWcwbkhEZTFHcDZUQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFnZytEeE9YQQpZREc4R0c2eHlsMUZmTUVNanROY0NhOVpzcVFTSG9GVzB0c1J0WDFSMHd6UzZya25mK3dya0FST3F5UHJ6WkQxCjlkN2pBR2Fwa2Y3VG5XOWthSXRkRTJaM1l2WlE4RWREZHFMMHVOL2FidXlhRlJlVmszNjRoRkpNZGo4RDFiUmMKT2YySFNxYWZ4QmxXdmVMdE5mYmNzcDhRVDROK3lPK2NoNi9NNE85a0FjVGc5dEprUXdnbmJaSk83Uk5qUHJkawpyVFJMYm5YUkh0UGVhWWFrTWNOTkN3SlEyR2xCQm5SUkJUMnRpUkFBWVJacHlTQ2NacHpodFBPdlRUdUsxbjFPCmF3NjR2OXhjU0tSSkg2ZEhUZVY0cThBYWpSSjdocE5rbGV6djd3OGNjMW5Na1hrcWtGZUk0K21yZC90Q1B2enkKcFZEWHJ0eWVYRkU4ekE9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', credentialsId: 'kubeconfig', serverUrl: 'https://127.0.0.1:51869/') {
                        sh '${HELM_BIN_DIR}/helm upgrade --install ${HELM_RELEASE_NAME} ${HELM_CHART_PATH} --set image.repository=${DOCKER_IMAGE}'
                    }
                }
            }
        }
    }
}
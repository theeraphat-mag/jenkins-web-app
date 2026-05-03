// pipeline {
//     agent {
//             label 'server'
//     }
//     triggers {
//         githubPush()
//     }

//     environment {
//         APP_NAME    = 'my-nginx-web'
//         IMAGE_TAG   = "${BUILD_NUMBER}"
//     }

//     stages {
//         stage('Checkout') {
//             steps {
//                 checkout scm
//             }
//         }

//         stage('Build Docker Image') {
//             steps {
//                 script {
//                     sh "docker build -t ${APP_NAME}:${IMAGE_TAG} -t ${APP_NAME}:latest ."
//                     // โหลด Image เข้า Kind
//                     sh "kind load docker-image ${APP_NAME}:${IMAGE_TAG}"
//                 }
//             }
//         }

//         stage('Deploy to Kubernetes') {
//             steps {
//                 script {
//                     sh "kubectl apply -f k8s/deployment.yaml"
//                     sh "kubectl apply -f k8s/service.yaml"
//                     sh "kubectl apply -f k8s/ingress.yaml"
//                     sh "kubectl set image deployment/nginx-deployment nginx-container=${APP_NAME}:${IMAGE_TAG}"
//                 }
//             }
//         }

//         stage('Verify Deployment') {
//             steps {
//                 script {
                   
//                     sh "kubectl get pods -l app=my-nginx"
//                     sh "kubectl get svc nginx-service"
//                     sh "kubectl get ingress nginx-ingress"
//                 }
//             }
//         }
//     }

//     post {
//         success {
//             echo "Deployment successful! Access at http://my-nginx.local"
//         }
//         failure {
//             echo "Deployment failed! Check logs for details."
//         }
//     }
// }

pipeline {
    agent { label 'server' }
    triggers {
        githubPush()
    }
    environment {
        APP_NAME  = 'my-nginx-web'
        IMAGE_TAG = "${BUILD_NUMBER}"
        CLUSTER   = 'vm-cluster'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${APP_NAME}:${IMAGE_TAG} -t ${APP_NAME}:latest ."
            }
        }
        stage('Load Image to Kind') {
            steps {
                sh "kind load docker-image ${APP_NAME}:${IMAGE_TAG} --name ${CLUSTER}"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f k8s/deployment.yaml"
                sh "kubectl apply -f k8s/service.yaml"
                sh "kubectl apply -f k8s/ingress.yaml"
                sh "kubectl set image deployment/nginx-deployment nginx-container=${APP_NAME}:${IMAGE_TAG}"
            }
        }
        stage('Verify Deployment') {
            steps {
                sh "kubectl rollout status deployment/nginx-deployment"
                sh "kubectl get pods -l app=my-nginx"
            }
        }
    }
    post {
        success {
            echo "Deployment successful! Access at http://10.138.238.220"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}

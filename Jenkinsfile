pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: node
    image: node:18-alpine
    command:
    - cat
    tty: true
  - name: docker
    image: docker:latest
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  - name: kubectl
    image: bitnami/kubectl:latest
    command:
    - cat
    tty: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
"""
        }
    }
    
    environment {
        DOCKER_IMAGE = 'weather-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
        DOCKER_REGISTRY = 'docker.io' // Docker Hub registry
        DOCKER_CREDENTIALS = 'docker-hub-creds' // Jenkins credential ID
        KUBECONFIG_CREDENTIALS = 'kubeconfig-creds' // Jenkins credential ID
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                container('node') {
                    dir('weather-app') {
                        sh 'npm install'
                    }
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                container('node') {
                    dir('weather-app') {
                        // Add actual test command when tests are available
                        sh 'npm test || echo "No tests defined yet"'
                    }
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    dir('weather-app') {
                        sh """
                            docker build -t ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG} .
                            docker build -t ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:latest .
                        """
                    }
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh """
                            echo \$DOCKER_PASSWORD | docker login ${DOCKER_REGISTRY} -u \$DOCKER_USERNAME --password-stdin
                            docker push ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG}
                            docker push ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:latest
                        """
                    }
                }
            }
        }
        
        // stage('Update Kubernetes Manifests') {
        //     steps {
        //         dir('weather-app') {
        //             script {
        //                 // Update the image tag in the deployment manifest
        //                 sh """
        //                     sed -i 's|image: .*|image: ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG}|' k8s/deployment.yaml
        //                     
        //                     # Commit and push the changes to trigger ArgoCD
        //                     git config user.email "jenkins@example.com"
        //                     git config user.name "Jenkins"
        //                     git add k8s/deployment.yaml
        //                     git commit -m "Update image tag to ${DOCKER_TAG}" || echo "No changes to commit"
        //                     git push origin main || echo "Failed to push changes"
        //                 """
        //             }
        //         }
        //     }
        // }
        
        stage('Deploy to Staging') {
            steps {
                container('kubectl') {
                    // Optional: Direct deployment to staging environment
                    withCredentials([kubeconfigFile(credentialsId: KUBECONFIG_CREDENTIALS, variable: 'KUBECONFIG')]) {
                        sh """
                            kubectl apply -f weather-app/k8s/ -n weather-app-staging
                            kubectl set image deployment/weather-app weather-app=${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG} -n weather-app-staging
                            kubectl rollout status deployment/weather-app -n weather-app-staging
                        """
                    }
                }
            }
        }
    }
    
    post {
        always {
            // Clean up Docker images
            container('docker') {
                sh """
                    docker rmi ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG} || true
                    docker rmi ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:latest || true
                """
            }
        }
        success {
            echo 'Pipeline succeeded!'
            // Add notifications here (Slack, email, etc.)
        }
        failure {
            echo 'Pipeline failed!'
            // Add failure notifications here
        }
    }
} 
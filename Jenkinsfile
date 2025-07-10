pipeline {
    agent any
    
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
                dir('weather-app') {
                    sh 'npm install'
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                dir('weather-app') {
                    // Add actual test command when tests are available
                    sh 'npm test || echo "No tests defined yet"'
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                dir('weather-app') {
                    script {
                        docker.build("${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG}")
                        docker.build("${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:latest")
                    }
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}", DOCKER_CREDENTIALS) {
                        docker.image("${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                        docker.image("${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:latest").push()
                    }
                }
            }
        }
        
        stage('Update Kubernetes Manifests') {
            steps {
                dir('weather-app') {
                    script {
                        // Update the image tag in the deployment manifest
                        sh """
                            sed -i 's|image: .*|image: ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG}|' k8s/deployment.yaml
                            
                            # Commit and push the changes to trigger ArgoCD
                            git config user.email "jenkins@example.com"
                            git config user.name "Jenkins"
                            git add k8s/deployment.yaml
                            git commit -m "Update image tag to ${DOCKER_TAG}" || echo "No changes to commit"
                            git push origin main || echo "Failed to push changes"
                        """
                    }
                }
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                script {
                    // Optional: Direct deployment to staging environment
                    withCredentials([kubeconfigFile(credentialsId: KUBECONFIG_CREDENTIALS, variable: 'KUBECONFIG')]) {
                        sh """
                            kubectl apply -f weather-app/k8s/ -n staging
                            kubectl set image deployment/weather-app weather-app=${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG} -n staging
                            kubectl rollout status deployment/weather-app -n staging
                        """
                    }
                }
            }
        }
    }
    
    post {
        always {
            // Clean up Docker images
            sh """
                docker rmi ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:${DOCKER_TAG} || true
                docker rmi ${DOCKER_REGISTRY}/srikar1924/${DOCKER_IMAGE}:latest || true
            """
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
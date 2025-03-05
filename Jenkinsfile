pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/AdityaWaikar/pi.git' // Repository URL
        DOCKER_IMAGE = 'us-docker.pkg.dev/hardy-clover-447804-t3/docker-test/myname:a2' // Artifact Registry URL with image name
        TARGET_VM_IP = '10.138.0.4'
        CONTAINER_PORT = '5000'
        CONTAINER_NAME = 'myapp'
        GOOGLE_CREDENTIALS = '12' // Jenkins Google Service Account credentials
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Clone the Git repository
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }

        stage('Authenticate to Artifact Registry') {
            steps {
                script {
                    // Use Google Cloud SDK to authenticate (replace with proper authentication for your registry)
                    withCredentials([file(credentialsId: GOOGLE_CREDENTIALS, variable: 'GOOGLE_AUTH')]) {
                        sh '''
                            gcloud auth activate-service-account --key-file=$GOOGLE_AUTH
                            gcloud auth configure-docker --quiet
                            gcloud auth configure-docker us-docker.pkg.dev
                        '''
                    }
                }
            }
        }

        stage('Push Docker Image to Artifact Registry') {
            steps {
                script {
                    // Push the Docker image to the registry
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }

//     stage('Deploy Docker Container on VM') {
//     steps {
//         script {
//             // Use SSH to access the target VM
//             sshagent(['1002']) {
//                 sh '''
//                 ssh -o StrictHostKeyChecking=no jenkins@10.138.0.4 << EOF
//                     docker pull ${DOCKER_IMAGE}
//                     docker stop myname || true
//                     docker rm myname || true
//                     docker run -d -p 5000:5000 --name myname ${DOCKER_IMAGE}
//                 EOF
//                 '''
//             }
//         }
//     }
// }
   stages {
        stage('Deploy Docker Container on VM') {
            steps {
                script {
                    // Use SSH Agent credentials to connect to the target VM
                    sshagent(['1002']) {
                        sh '''
                            # Connect to the target VM and run Docker commands
                            ssh -o StrictHostKeyChecking=no ${TARGET_VM_USER}@${TARGET_VM_IP}
                                # Pull the Docker image
                                docker pull ${DOCKER_IMAGE}
                                # Run the new Docker container
                                docker run -d \
                                    -p ${CONTAINER_PORT}:${CONTAINER_PORT} \
                                    --name ${CONTAINER_NAME} \
                                    ${DOCKER_IMAGE}'''
                                
                                # Stop and remove existing container if it exists
                                docker stop ${CONTAINER_NAME} || true
                                docker rm ${CONTAINER_NAME} || true
                                
                                
                                
                                # Optional: Verify container is running
                                docker ps | grep ${CONTAINER_NAME}
                            '''
                    }
                }
            }
        }
    }
    }
}


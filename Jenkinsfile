pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/AdityaWaikar/pi.git' // Repository URL
        DOCKER_IMAGE = 'us-docker.pkg.dev/hardy-clover-447804-t3/docker-test/myname:a2' // Artifact Registry URL with image name
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
                        '''
                    }
                }
            }
        }

        /*stage('Push Docker Image to Artifact Registry') {
            steps {
                script {
                    // Push the Docker image to the registry
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }*/

        /*stage('Deploy Docker Container') {
            steps {
                script {
                    // Remove the container if it already exists
                    //sh 'docker rm -f nginx-app || true'

                    // Run the container with the pushed image
                    sh 'docker run -d -p 5000:5000 --name myname ${DOCKER_IMAGE}'
                }
            }
        }*/
    }
}


pipeline {
    agent any
    environment {
        VENV_DIR = 'venv'
        DOCKER_IMAGE = 'syedsharmila/flask-api:latest'
        //DOCKER_IMAGE_TAG = "${DOCKER_IMAGE}:${BUILD_NUMBER}" // Use BUILD_NUMBER for versioning
        FLASK_APP_PORT = '5310'
        SERVER_IP = '18.132.73.146' // Replace with your server's public IP
        BRANCH_NAME = "${env.BRANCH_NAME}" // Jenkins built-in environment variable
    }
    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/sharmila-syed2024/MultiBranchPipeline.git', branch: 'main'
            }
        }
        stage('Set Up Virtual Environment') {
            steps {
                script {
                    // Create a virtual environment
                    sh 'python3 -m venv ${VENV_DIR}'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    // Activate virtual environment, upgrade pip, and install dependencies
                    sh '''
                        source ${VENV_DIR}/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt
                    '''
                }
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    // Activate virtual environment and run pytest
                    sh '''
                        source ${VENV_DIR}/bin/activate
                        pytest test_app.py --junitxml=test-results.xml
                    '''
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub
                    withCredentials([usernamePassword(credentialsId: 'ecr-credentials', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        sh 'echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin'
                    }
                    // Push the image
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }
        stage('Verify Deployment Files') {
            steps {
                script {
                    // Verify that the deployment files exist
                    sh 'ls -al k8s/'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Apply the Kubernetes deployment and service files
                    sh '''
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                    '''
                }
            }
        }
        // Step 5: Handle Jenkins Credentials
        // stage('Handle Jenkins Credentials') {
        //     steps {
        //         script {
        //             withCredentials([usernamePassword(credentialsId: 'ecr-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
        //                 sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com'
        //             }
        //         }
        //     }
        }
                    
        // Step 7: Project Relationship (Upstream & Downstream Jobs)
        stage('Trigger Downstream Job') {
            steps {
                script {
                    build job: 'Downstream_Job_Name'
                }
            }
        }
                    
        // Step 9: Deploy to Docker Hub or ECR/ACR
        stage('Deploy to Docker Registry') {
            steps {
                script {
                    sh 'docker tag ${DOCKER_IMAGE} <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/${DOCKER_IMAGE}'
                    sh 'docker push <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/${DOCKER_IMAGE}'
                }
            }
        }
    }
//Step 8: Success/Failure Notification to Email
 post {
        success {
            mail to: 'syed.begum@informationtechconsultants.co.uk', subject: 'Jenkins Job Succeeded', body: 'The Jenkins job has successfully completed.'
            // Output the full URL to access the Flask API
            echo "Build succeeded. The Flask API is running at http://${SERVER_IP}:${FLASK_APP_PORT}/data"
        }
      // Step 6: Jenkins Failed Job Alerts
        failure {
            mail to: 'syed.begum@informationtechconsultants.co.uk', subject: 'Jenkins Job Failed', body: 'The Jenkins job has failed. Please check the logs.'
        }
 }


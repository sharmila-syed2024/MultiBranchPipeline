pipeline {
    agent any
    environment {
        VENV_DIR = 'venv'
        DOCKER_IMAGE = 'syedsharmila/flask-api:latest'
        FLASK_APP_PORT = '5310'
        SERVER_IP = '18.132.73.146' // Replace with your server's public IP
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
                    sh 'python3 -m venv ${VENV_DIR}'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
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
                    withCredentials([usernamePassword(credentialsId: 'ecr-credentials', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        sh 'echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin'
                    }
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }
        stage('Verify Deployment Files') {
            steps {
                script {
                    sh 'ls -al k8s/'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh '''
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                    '''
                }
            }
        }
        stage('Trigger Downstream Job') {
            steps {
                script {
                    build job: 'Downstream_Job_Name'
                }
            }
        }
        stage('Deploy to Docker Registry') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'ecr-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com'
                    }
                    sh '''
                        docker tag ${DOCKER_IMAGE} <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/${DOCKER_IMAGE}
                        docker push <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/${DOCKER_IMAGE}
                    '''
                }
            }
        }
    }
    post {
        success {
            mail to: 'syed.begum@informationtechconsultants.co.uk', 
                 subject: 'Jenkins Job Succeeded', 
                 body: "The Jenkins job has successfully completed.\n\nThe Flask API is running at http://${SERVER_IP}:${FLASK_APP_PORT}/data"
        }
        failure {
            mail to: 'syed.begum@informationtechconsultants.co.uk', 
                 subject: 'Jenkins Job Failed', 
                 body: 'The Jenkins job has failed. Please check the logs.'
        }
    }
}

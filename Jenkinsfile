pipeline {
    agent any
    tools {
        maven 'maven' // Use default Maven tool
    }
    
    environment {
        GITHUB_REPO = 'https://github.com/uthejreddy1/springbootgit.git'
        IMAGE_NAME = 'springboot:latest'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                // Ensure the branch is set to "main"
                git branch: 'main', url: "${GITHUB_REPO}"
            }
        }
        
        stage('Build Maven') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t springboot .'
                }
            }
        }
        
        stage('Authenticate with AWS ECR') {
            steps {
                script {
                    sh '''
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 992382642635.dkr.ecr.us-east-1.amazonaws.com
                    '''
                }
            }
        }
        
        stage('Tag Docker Image') {
            steps {
                script {
                    sh 'docker tag springboot:latest 992382642635.dkr.ecr.us-east-1.amazonaws.com/springboot:latest'
                }
            }
        }
        
        stage('Push Image to ECR') {
            steps {
                script {
                    sh 'docker push 992382642635.dkr.ecr.us-east-1.amazonaws.com/springboot:latest'
                }
            }
        }
        
        stage('Pull Docker Image from ECR') {
            steps {
                script {
                    sh '''
                        docker pull 992382642635.dkr.ecr.us-east-1.amazonaws.com/springboot:latest
                    '''
                }
            }
        }
        
        stage('Integrate EKS Cluster with Jenkins') {
            steps {
                script {
                    sh '''
                        aws eks update-kubeconfig --name demo-cluster --region us-east-1
                    '''
                }
            }
        }
        
        stage('Deploy to EKS') {
            steps {
                script {
                    sh 'kubectl apply -f k8s.yaml'
                }
            }
        }
    }
}

pipeline {
    agent any
    tools {
        maven 'maven' // Use default maven tool
    }
    
    environment {
        //GITHUB_REPO = 'https://github.com/uthejreddy1/springbootgit.git'
       IMAGE_NAME = 'springboot:latest'
        //KUBE_CONFIG = '/home/ec2-user/.kube/config'
    }
    
    stages {
        
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                }
            }
        
        stage('Build Maven') {
            steps {
                //checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Java-Techie-jt/devops-automation.git']]])
                sh 'mvn clean package'
            }
        }
        
        stage('Build docker image') {
            steps {
                script {
                    sh 'docker build -t springboot .'
                }
            }
        }
        stage('Authenticate with AWS ECR') {
            steps {
                script {
                    // AWS CLI should automatically use the IAM role attached to the Jenkins machine
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
        stage('Push image to ECR') {
            steps {
                script {
                    sh 'docker push 992382642635.dkr.ecr.us-east-1.amazonaws.com/springboot:latest'
                }
            }
        }
                stage('Pull Docker Image from ECR') {
            steps {
                script {
                    // Pull the Docker image from ECR
                    sh """
                    docker pull 992382642635.dkr.ecr.us-east-1.amazonaws.com/springboot:latest
                    """
                }
            }
        }
        
        stage('Integrate eks cluster with Jenkins') {
            steps {
                script {
                    // Set the kubeconfig file path to authenticate with EKS
                    //withEnv(["KUBECONFIG=/home/ec2-user/.kube/config"]) {
                        // Apply Kubernetes manifests from the 'k8s' folder
                        sh ("aws eks update-kubeconfig  --name demo-cluster --region us-east-1")
                    }
                }
            }
        

        stage('Deploy to EKS') {
            steps {
                script {
                    // Set the kubeconfig file path to authenticate with EKS
                    //withEnv(["KUBECONFIG=/home/ec2-user/.kube/config"]) {
                        // Apply Kubernetes manifests from the 'k8s' folder
                        sh "kubectl apply -f k8s.yaml"
                        //sh "kubectl apply -f k8s/service.yaml"
                    }
                }
            }
        }

    }

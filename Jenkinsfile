pipeline {
  agent any
  environment {
        AWS_ACCOUNT_ID="718000810402"
        AWS_DEFAULT_REGION="ap-south-1"
        IMAGE_REPO_NAME="nodejs"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "718000810402.dkr.ecr.ap-south-1.amazonaws.com/nodejs"
	}
  
   tools {nodejs "node"}

      stages {
        
    stages {
        
         stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
                }
                 
            }
        }
  stages {
    stage("Clone code from GitHub") {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GITHUB_CREDENTIALS', url: 'https://github.com/KPhaniPrasad/nodejsapp.git']])
                }
            }
        }
     
    stage('Node JS Build') {
      steps {
        sh 'npm install'
      }
    }
  
     stage('Build Node JS Docker Image') {
            steps {
                script {
                  sh 'docker build -t devopshint/node-app-1.0'
                }
            }
        }


           // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
         }
        }
      }
    
     stage('Deploying Node App to Kubernetes') {
      steps {
        script {
          sh ('aws eks update-kubeconfig --name sample --region ap-south-1')
          sh "kubectl get ns"
          sh "kubectl apply -f nodejsapp.yaml"
        }
      }
    }

  }
}

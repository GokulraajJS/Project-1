pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "150878085644.dkr.ecr.ap-south-1.amazonaws.com/test"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/yasminjeelani/EKS-CICD.git']]])     
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 150878085644.dkr.ecr.ap-south-1.amazonaws.com/test'
                sh 'docker push 150878085644.dkr.ecr.ap-south-1.amazonaws.com/test'
         }
        }
      }

       stage('K8S Deploy') {
            steps{   
            script {
                sh ('kubectl apply -f  eks-deploy-k8s.yaml')
                
            }

       }
    }
}
}

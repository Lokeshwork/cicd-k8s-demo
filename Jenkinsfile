pipeline {
	    agent {label 'kubernetes'}
    	environment {
	    AWS_ACCOUNT_ID="115069646213"
	    AWS_DEFAULT_REGION="us-east-1"
	    IMAGE_REPO_NAME="simple-time-app"
	    IMAGE_TAG="kubernetesdeploy"
	    REPO_URL = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/simple-time-app"
	    YAML_FILE = "deployment.yml"
  }

  stages {
    stage('Compile') {
      steps {
        git branch: 'master', url: 'https://github.com/Lokeshwork/cicd-k8s-demo.git'
        script{
                def mvnHome = tool name: 'MAVEN_HOME', type: 'maven'
                sh "${mvnHome}/bin/mvn package"
        }
      }
    }
    stage('Building Docker Image') {
      steps{
        script {
          sh "docker build -t ${REPO_URL}:${IMAGE_TAG} ."
        }
      }
    }
  stage('logging to AWS ECR'){
		steps{
			script {
					sh """aws ecr get-login-password \
			--region ${AWS_DEFAULT_REGION} \
			| docker login \
			--username AWS \
			--password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
			}
		}
	}
	
	stage('ECR push') {
		steps{
			script {
			sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
			}
		}
	}
   stage('Deploy to Kubernetes') {
        steps{
            sh 'kubectl apply -f ${YAML_FILE}'
       }
    }
  }
}

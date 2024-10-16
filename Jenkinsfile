pipeline {
    agent any
    environment {
        ECR_REPO_URI = '123456789012.dkr.ecr.us-east-1.amazonaws.com/t2s-services-repo'
    }
    stages {
        stage('Build') {
            steps {
                script {
                    docker.build("$ECR_REPO_URI:$BUILD_NUMBER")
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    withAWS(region: 'us-east-1') {
                        sh "aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_REPO_URI"
                        docker.image("$ECR_REPO_URI:$BUILD_NUMBER").push()
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    kubernetesDeploy(
                        configs: 'k8s/deployment.yaml',
                        kubeconfigId: 'kubeconfig'
                    )
                }
            }
        }
    }
}

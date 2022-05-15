pipeline {
    agent any
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "sowmya/image"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
             
            steps {
                script {
                     
                    app = docker.build("sowmya/image")
                     
                }
            }
        }
        stage('Push Docker Image') {
             
            steps {
                script {
                     sh"echo image pushed to hub"
                }
            }
        }
        stage('CanaryDeploy') {
             
            environment { 
                CANARY_REPLICAS = 1
            }
            steps {
                 sh "k8s create Deployement"
            }
        }
        stage('DeployToProduction') {
            
            
            environment { 
                CANARY_REPLICAS = 0
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}

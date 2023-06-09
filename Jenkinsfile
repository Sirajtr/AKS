pipeline {
  tools {
        maven 'Maven3'
    }
    agent any
        environment {
        //once you create ACR in Azure cloud, use that here
        registryName = "sirajtr"
        //- update your credentials ID after creating credentials for connecting to ACR
        registryCredential = 'acr'
        dockerImage = ''
        registryUrl = 'sirajtr.azurecr.io'
    }
    
    stages {
        stage('checkout') {
            steps {
               git 'https://github.com/Sirajtr/AKS'
            }
        }
        
        stage ('Build') {
        steps {
            sh 'mvn clean install'           
        }
     }
     
    stage ('Build Docker image') {
        steps {
                script {
                    dockerImage = docker.build registryName
                }
            }
        }
        
    // Uploading Docker images into ACR
        stage('Upload Image to ACR') {
         steps{   
             script {
                docker.withRegistry( "http://${registryUrl}", registryCredential ) {
                dockerImage.push()
                }
            }
          }
        }
        
        stage ('K8S Deploy') {
          steps {
            script {
                withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
                sh ('kubectl apply -f  jenkins-aks-deploy-from-acr.yaml')
                }
            }
        }
     }
    }
}

pipeline {
    agent any

    environment {
        // ACR and AKS details
        acrName = "aspnetimgg"
        aksName = "asp-cluster"
        aksResourceGroup = "aspnet-rg"
        
        imageTag = "asp"
        gitRepo = "https://github.com/vaishnavi-g06/aspnetapp.git"
        gitBranch = "main"
        dockerfilePath = "Dockerfile"
        deploymentName = "mydeployment"
        containerName = "mycontainer"
    }

    stages {
        stage ('checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/vaishnavi-g06/aspnetapp.git']]])
            }
        }
        

        stage('Build and Push') {
            steps {
                // Build Docker image
                sh "docker build -t ${acrName}.azurecr.io/myimage:${imageTag} -f ${dockerfilePath} ."

                // Authenticate with ACR
                azureLogin([azureSubscription(credentialsId: 'reg_cred', subscriptionId: 'ec3f4ce6-ba2e-44bc-af3c-e8ffa0f871ae')])

                // Push image to ACR
                sh "docker push ${acrName}.azurecr.io/myimage:${imageTag}"
            }
        }

        stage('Deploy to AKS') {
            steps {
                // Authenticate with AKS
                azureAKS([azureSubscription(credentialsId: 'acr_cred', resourceGroup: aksResourceGroup, aksName: aksName)])

                // Deploy image to AKS
                sh "kubectl set image deployment/${deploymentName} ${containerName}=${acrName}.azurecr.io/myimage:${imageTag} "
            }
        }
    }
    }

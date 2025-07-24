pipeline {
    agent { label 'slave01' }
    tools {
        maven 'Maven03'
    }
    environment {
        registryName = 'jangoregistry'
        registryUrl = 'jangoregistry.azurecr.io'
        registryCredential = 'c11253f0-6a37-453d-ad1a-8481993d8f3d'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: 'cfea1108-9dd2-4dae-b9dd-f37feade0b8e', poll: false, url: 'https://github.com/jerinvarghese1993/Shopping-Cart.git'
            }
        }
        stage('Build the artifacts') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        stage('Build the image using docker') {
            steps {
                script {
                    dockerImage = docker.build(env.registryName, "-f docker/Dockerfile .")
                }
             }
        }
        stage('Upload to ACR') {
            steps {
                script {
                    docker.withRegistry("https://${registryUrl}", registryCredential) {
                    dockerImage.push("latest")
                    }
                }
            }
        }
        stage('Deploy to ACR') {
            steps {
                script {
                     withKubeConfig(caCertificate: '', clusterName: 'jango-AKS', contextName: '', credentialsId: 'kube-config', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'jango-aks-dns-lye6ebog.hcp.westus3.azmk8s.io') {
                        sh 'kubectl apply -f deploymentservice.yml'
                    }
                }
            }
        }
    }
}

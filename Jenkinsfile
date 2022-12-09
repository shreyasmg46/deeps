#!groovy
pipeline {
    environment {
        registry = "deepashreekl/rps-ant"
        registryCredentials = 'docker-credentials'
    }
    agent {label 'slave'}
    stages {
        stage('Clone the Git Repository') {
            steps {
                git credentialsId: 'git-credentials', url: 'https://github.com/Shreedeepa/rps-ant.git'
            }
        }
        stage('Building docker image') {
            steps {
                sh 'echo Building docker image'
                script {
                    buildDate = new Date()
                    image = docker.build("${registry}:$BUILD_NUMBER")
                }
            }
        }
        stage('Docker Registry-Push') {
            steps {
                sh 'echo Registry-push'
                script {
                    docker.withRegistry('', registryCredentials) {
                        image.push()
                        image.push('latest')
                    }
                }
            }
        }
        stage('CleanUp workspace') {
            steps {
                sh ' echo CleanUp'
                sh "docker rmi ${registry}:$BUILD_NUMBER"
                sh "docker rmi ${registry}:latest"
            }
        }
    }
}


stage('Deploy to EKS Cluster'){
    steps{
       withAWS(credentials:'awscreds', region:'us-east-1'){
           sh 'aws eks update-service --cluster ${cluster} --service ${service} --force deployment'
       }
     }
}
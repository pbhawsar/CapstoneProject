pipeline {
    agent any
    tools{
        maven 'Maven'
    }
    stages {
        
        stage('Cleanup') {
            steps {
                // Cleans the workspace on the current node/agent
                cleanWs()
            }
        }
        
        stage('Checkout') {
            steps {
                // Checkout the git repo
               checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GITHUB_ACCESS_TOKEN', url: 'https://github.com/pbhawsar/CapstoneProject.git']])
            }
        }
        
        stage('Build') {
            steps {
                // Maven build
                 sh 'mvn clean install'
            }
        }
        
        stage('Build Docker Image') {
            steps{
                script{
                    // Build docker image 
                    sh 'docker build -t bhawsar/tempapp:${BUILD_NUMBER} .'
                }
            }
        }
        
        stage('Push Docker Image to DockerHub') {
            steps{
                script {
                    // Login to docker hub and push docker image 
                    withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'DOCKER_HUB_PWD')]) {
                     sh 'docker login -u bhawsar -p ${DOCKER_HUB_PWD}'
                     sh 'docker push bhawsar/tempapp:${BUILD_NUMBER}'
                    }
                }
            }
        }
        
        stage('Docker logout') {
            steps{
                script {
                    // Logout of docker hub
                    sh 'docker logout'
                }
            }
        }
        
        stage('Clean Up Docker Image') {
            steps{
                script {
                    // Delete the docker image
                    sh 'docker rmi bhawsar/tempapp:${BUILD_NUMBER}'
                }
            }
        }
   
        stage('Execute Ansible Playbook'){
            steps{
               script {
                   // Ansible playbook to depoy the docker image on EC2 instance 
                   sh 'ansible-playbook ansible-playbook.yml --extra-vars "container_name=insureme image_name=docker.io/bhawsar/tempapp:${BUILD_NUMBER}"'
               }
            }
        }
        
        
    }
}

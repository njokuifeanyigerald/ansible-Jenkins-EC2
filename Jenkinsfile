pipeline {
    agent any
    tools {
        maven 'mvn'
    }
    stages{
        stage('GIT checkout') {
            steps {
                git credentialsId: 'github_cred', url: 'https://github.com/njokuifeanyigerald/ansible-Jenkins-EC2.git'
           }
       }
          stage('Build Package') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Docker build and Tag') {
            steps{
                sh 'docker build -t ${JOB_NAME}:v1.${BUILD_NUMBER} .'
                sh 'docker tag ${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:v1.${BUILD_NUMBER} '
                sh 'docker tag ${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:latest '
            }
        }
        stage('push conatiner') {
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubcred')]) {
                  sh 'docker login -u bopgeek -p ${dockerhubcred}'
                  sh 'docker push bopgeek/${JOB_NAME}:v1.${BUILD_NUMBER}'
                  sh 'docker push bopgeek/${JOB_NAME}:latest'
                  sh 'docker rmi ${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:v1.${BUILD_NUMBER} bopgeek/${JOB_NAME}:latest'
                }      
            }
        }
        stage('Docker Deploy') {
            steps{
                // ansiblePlaybook credentialsId: 'ansible-host', disableHostKeyChecking: true, inventory: 'invemtory.txt', playbook: 'deploy.yml'
                ansiblePlaybook credentialsId: 'ansible-host', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory.txt', playbook: 'ansible.yml'
                // ansiblePlaybook credentialsId: 'ansible-host', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory.txt', playbook: 'deploy.yml'
            }
        }   

            
    }
}

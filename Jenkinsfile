pipeline {
    agent any
    tools {
        maven 'mvn'
    }
    stages{
        stage('GIT checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/shivamsinghgit/DevOps-Capstone-Project-B-Safe']]])
           }
       }
          stage('Maven Build Package') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Docker build and Tag') {
            steps{
                sh 'docker build -t ${JOB_NAME}:v1.${BUILD_NUMBER} .'
                sh 'docker tag ${JOB_NAME}:v1.${BUILD_NUMBER} shivam1502/${JOB_NAME}:v1.${BUILD_NUMBER} '
                sh 'docker tag ${JOB_NAME}:v1.${BUILD_NUMBER} shivam1502/${JOB_NAME}:latest '
            }
        }
        stage('Push conatiner to docker hub') {
            steps{
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                  sh 'docker login -u shivam1502 -p ${dockerhubpwd}'
                  sh 'docker push shivam1502/${JOB_NAME}:v1.${BUILD_NUMBER}'
                  sh 'docker push shivam1502/${JOB_NAME}:latest'
                  sh 'docker rmi ${JOB_NAME}:v1.${BUILD_NUMBER} shivam1502/${JOB_NAME}:v1.${BUILD_NUMBER} shivam1502/${JOB_NAME}:latest'
                }      
            }
        }
        stage('Deploy on docker machine'){
            steps{
                
				sshagent(['sshdev']) {
				    sh "ssh -o StrictHostKeyChecking=no ec2-user@34.238.153.147 docker rm -f nginx"
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@34.238.153.147 docker rmi shivam1502/b-safe || true"
					sh "ssh -o StrictHostKeyChecking=no ec2-user@34.238.153.147 docker run -d --name nginx -p 80:80 shivam1502/b-safe"
            }
			}
        }
    }
}

pipeline {
    agent any
    tools {
        nodejs 'Node'
    }
    stages {
        stage('Build') {
            steps {
                // checkout scmGit(branches: [[name: '*/Dev']], extensions: [], userRemoteConfigs: [[credentialsId: 'Github', url: 'https://github.com/Induprojects/Capstone-Project.git']])
                sh 'npm install'
                // sh 'npm run build'
            }
        }
        stage('Test') {
            steps {
                // sh 'npm run test'
                echo "Test"
            }
        }
       stage('Build Image') {
            steps { 
                sh 'docker build -t reactimage:v1 .'
                sh 'docker tag reactimage:v1 venkatesh7708/capstone-pipeline:v1'
            }    
       }
       stage('Docker login') {
            steps { 
                withCredentials([usernamePassword(credentialsId: 'DockerCred', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                sh "echo $PASS | docker login -u $USER --password-stdin"
                sh 'docker push venkatesh7708/capstone-pipeline:v1'
                }
            }
       }
       stage('Deploy') {
            steps {  
                script {
                   def dockerCmd = 'docker run -itd --name My-first-container -p 80:5000 venkatesh7708/capstone-pipeline:v1'
                   sshagent(['sshkeypair']) {
                   sh "ssh -o StrictHostKeyChecking=no ubuntu@54.86.64.88 ${dockerCmd}"
                   }
                }
            }
       }
    }
    post {  
         always {  
             echo 'This will always run'  
         }  
         success {   
            echo "========Deploying executed successfully========"
            emailext attachLog: true, body: "<b>Example</b><br>Project: ${env.JOB_NAME}", from: 'dogcat1130@gmail.com',compressLog: true, mimeType: 'text/html', replyTo: '', subject: "Deploy Successfull Project ${env.JOB_NAME}", to: "dogcat1130@gmail.com";
         }  
         failure {  
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> Stage Name: $last_started <br> URL de build: ${env.BUILD_URL}", cc: 'kousigowthaman99@gmail.com', charset: 'UTF-8', from: 'dogcat1130@gmail.com', mimeType: 'text/html', replyTo: '', subject: "Deployment failed for Project -> ${env.JOB_NAME}", to: "dogcat1130@gmail.com";  
         }  
         unstable { 
             echo 'This will run only if the run was marked as unstable'  
         }  
         changed {  
             echo 'This will run only if the state of the Pipeline has changed'   
             echo 'For example, if the Pipeline was previously failing but is now successful'  
         }  
     }
}

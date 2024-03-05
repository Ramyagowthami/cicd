pipeline {
    agent any
    stages{
        stage('Build Maven'){
            steps{
                git url:'https://github.com/Ramyagowthami/cicd.git/', branch: "master"
               sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t ramyabharath/endtoendproject:v1 .'
                }
            }
        }
          stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'pipeline', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push ramyabharath/endtoendproject:v1'
                }
            }
        }
        stage("ssh into k8s-master") {
            steps{
                def remote=[:]
                remote.name='k8s-master'
                remote.host=''
                remote.user='ubuntu'
                remote.Password='ubuntu'
                remote.allowAnyHosts=true
            }
        }
         stage('deployment') { 
             steps{
                 sshPut remote: remote, from: 'deploymentservice.yaml', into '.' 
             }
        }
        
        stage('Deploy to k8s') {
            steps{
                sshCommand remote: remote, command: 'kubectl apply -f deploymentservice.yaml'
        
            
                   
                }
            }
        }
    }


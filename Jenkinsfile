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
                    sh 'docker build -t ramyabharath/project:v2 .'
                }
            }
        }
          stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhubpwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push ramyabharath/project:v2'
                }
            }
        }
        
        
        stage('Deploy to k8s'){
            when{ expression {env.GIT_BRANCH == 'origin/master'}}
            steps{
                script{
                     kubernetesDeploy (configs: 'deploymentservice.yaml' ,kubeconfigId: 'k8sconfigspwd')
                   
                }
            }
        }
    }
}

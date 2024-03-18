pipeline {
    agent any
    stages{
        stage('Build Maven'){
            steps{
                git url:'https://github.com/Ramyagowthami/cicd/', branch: "master"
               sh 'mvn clean install'
            }
        }
        stage('sonarqubeanalysis'){
            steps{
                withSonarQubeEnv('testsonarqube'){
                    dir ("target/devops.integration.jar"){
                        sh 'mvn sonar:sonar
                    }
                }
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t ramyabharath/cicd:v1 .'
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

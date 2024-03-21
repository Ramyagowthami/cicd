pipeline {
    agent any
    stages{
        stage ('code checkout'){
         steps{
             git credentialsId:'gittoken', url: 'https://github.com/Ramyagowthami/cicd-project.git'
            }   
        }
       stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonar', installationName: 'jenkins') {
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=nodetodo"
                }
            }
        }
        stage('Maven Build'){
            steps {
                sh 'mvn clean package'
            }
        }
        stage('build Docker image'){
            steps{
                script{
                    sh 'docker build -t ramyabharath/private:v1 .'
                }
            }
        }
        stage('Docker login'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                     sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push ramyabharath/private:v1'
                }
                
            }
        }
        
        stage('Deploy to k8s'){
            when{ expression {env.GIT_BRANCH == 'origin/master'}}
            steps{
                script{
                     kubernetesDeploy (configs: 'deploymentservice.yaml' ,kubeconfigId: 'kubeconfigId')
                   
                }
            }
        }
        }
}
        
    



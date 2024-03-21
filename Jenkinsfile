pipeline {
    agent any
    stages{
        stage ('code checkout'){
         steps{
             git url: 'https://github.com/Ramyagowthami/cicd.git'
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
        
         stage('Publish to Nexus') {
            steps{  
               dir('/var/lib/jenkins/workspace/sonarproject/target') {
               nexusArtifactUploader artifacts: [[artifactId: 'devops-integration', classifier: '', file: 'target/devops-integration.jar', type: 'jar']], credentialsId: 'nexus', groupId: 'com.truelearning', nexusUrl: '52.66.80.156:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'endproject', version: '0.0.1-SNAPSHOT'
           }
        }
         }
        stage('build Docker image'){
            steps{
                script{
                    sh 'docker build -t ramyabharath/private:t1.'
                }
            }
        }
        stage('Docker login'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                     sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push ramyabharath/private:t1'
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
        
    



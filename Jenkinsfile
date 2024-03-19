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
        
        stage('Stage Artifacts'){
            steps{
                script {
                    def server = Artifactory.server(url:http://65.0.7.224:8082/artifactory', credentialsId: 'jfrog')
                    def uploadSpec = """{
                    "files": [{
                    "pattern": "/var/lib/jenkis/workspace/target/devops-integration.jar",
                    "target": "testCICD/"
                    }]
                    }"""
                    server.upload(uploadSpec)
                }
            }
        }
        stage('build Docker image'){
            steps{
                script{
                    sh 'docker build -t ramyabharath/cicd:v11.01.'
                }
            }
        }
        stage('Docker login'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                     sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push ramyabharath/cicd:v11.01'
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
        
    



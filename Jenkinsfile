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
        stage("Quality gate") {
            steps {
                waitForQualityGate abortPipeline: true
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
                    def server = jfrog.server 'test-jfrog'
                    def uploadSpec = """{
                    dir ('cd /var/lib/jenkins/workspace/sonarproject/target/devops-integration.jar)
                    "files": [{
                    "pattern": "/target/devops-integration.jar",
                    "target": "testCICD"
                    }]
                    }"""
                    server.upload(uploadSpec)
                }
            }
        }
        stage('build Docker image'){
            steps{
                script{
                    sh 'docker build -t ramyabharath/cicd:v11.01 .'
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
                     kubernetesDeploy (configs: 'deploymentservice.yaml' ,kubeconfigId: 'k8spwd-ssh')
                   
                }
            }
        }
        }
}
        
    



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
                withSonarQubeEnv('sonar'){
                        sh 'cd /var/lib/jenkis/workspace/devops-integration.jar && mvn sonar:sonar'
                }
            }
        }
        stage('Stage Artifacts'){
            steps{
                script {
                    def server = jfrog.server 'test-jfrog'
                    def uploadSpec = """{
                    "files": [{
                    "pattern": "devops-integration/target/devops-integration.jar",
                    "target": "testCICD"
                    }]
                    }"""
                    server.upload(uploadSpec)
                }
            }
        }
        
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t ramyabharath/CICDproject:v2 .'
                }
            }
        }
          stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push ramyabharath/CICDproject:v2'
                }
            }
        }
        
          stage('Deploy to k8s'){
            when{ expression {env.GIT_BRANCH == 'origin/master'}}
            steps{
                script{
                     kubernetesDeploy (configs: 'deploymentservice.yaml' ,kubeconfigId: 'k8sconfigs-pwd')
}
}
}
}
}
    



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
    }
}

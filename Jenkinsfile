pipeline {
    agent any
    environment {
        PATH = "$PATH:/usr/share/maven/bin"
    }

    stages {

        stage('Git Checkout'){
            steps{
               git branch: 'main', url: 'https://github.com/smartobi/webapp-sonarqube.git' 
            }
        }

        stage('Unit Testing'){
            steps{
               sh 'mvn test' 
            }
        }

        stage('Integration testing'){
            steps{
               sh 'mvn verify -DskipUnitTests' 
            }
        }

        stage('Maven Build'){
            steps{
               sh 'mvn clean install' 
            }
        }

        stage('SonarQube analysis'){
            steps{
                   script{
                     withSonarQubeEnv(credentialsId: 'keytoken') {
                     sh 'mvn clean package sonar:sonar'
                 }
                }
               
            }

        
        }
       stage("Quality Gate"){
          timeout(time: 1, unit: 'HOURS') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                  emailext body: 'Your code was failed due to sonarqube quality gate', subject: 'Jenkins Failed Report', to: 'smartobi2022@gmail.com'
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"

              }
          }
      }
        
    }
}

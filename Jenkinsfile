pipeline {
    agent any
    environment {
        PATH = "$PATH:/usr/share/maven/bin"
    }

    stages {

        stage('Git Checkout'){
            steps{
               git branch: 'main', url: 'https://github.com/smartobi/webapp-sonarqube'
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
                     withSonarQubeEnv(credentialsId: 'keysecret') {
                     sh 'mvn clean package sonar:sonar'
                 }
                }
               
            }

        
        }

        stage('Quality Gate Status'){
            steps{
                   script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'keysecret'
                    
                 }
                }
               
        }

        
        
        
    }
}

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
                     withSonarQubeEnv(credentialsId: 'keytoken') {
                     sh 'mvn clean package sonar:sonar'
                 }
                }
               
            }

        
        }
        

        stage('Quality Gate Status'){
            steps{
                   script{
                   waitForQualityGate abortPipeline: false, credentialsId: 'keytoken'
                    
                 }
                }
               
        }
        stage('Uploading war file to Nexus'){
            steps{
                script{
                    def readPomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "webapp-snapshot" : "webapp-release"
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: 'MyWebApp', 
                            classifier: '', 
                            file: 'target/MyWebApp.war', 
                            type: 'war'
                            ]
                            ], 
                            credentialsId: 'nexusserverlogin', 
                            groupId: 'com.mkyong', 
                            nexusUrl: '44.211.86.199:8081', 
                            nexusVersion: 'nexus3', 
                            protocol: 'http', 
                            repository: nexusRepo, 
                            version: "${readPomVersion.version}"
                }
            }
        }

        
        
        
    }
}

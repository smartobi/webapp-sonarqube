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
        stage('Uploading jar file to Nexus'){
            steps{
                script{
                    def readPomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "webapp-snapshot" : "webapp-release"
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: 'MyWebApp', 
                            classifier: '', 
                            file: 'target/MyWebApp.jar', 
                            type: 'jar'
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

        stage('Docker Image Build'){
            steps{
                script{
                    sh 'docker image build -t $JOB_NAME:1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:1.$BUILD_ID smartcloud2022/$JOB_NAME:1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:1.$BUILD_ID smartcloud2022/$JOB_NAME:latest'
                }
            }
        }

        stage('Push image to docker hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_hub_cred')]) {
                       sh 'docker login -u smartcloud2022 -p ${docker_hub_cred}'
                       sh 'docker image push smartcloud22/$JOB_NAME:1.$BUILD_ID'
                       sh 'docker image push smartcloud22/$JOB_NAME:latest'
                     }
                }
            }
        }

        
        
        
    }
}

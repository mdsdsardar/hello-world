pipeline{
    agent any
    stages{
        stage("Github automation"){
            steps{
                git branch: 'master', url: 'https://github.com/mdsdsardar/hello-world.git'
            }
        }
        stage("Maven Build"){
            tools {
                maven 'Maven' 
            }
            steps{    
                sh 'mvn clean install package'
            }
        }
        stage("SonarQube analysis"){
            tools {
                maven 'Maven'
            }
            steps{ 
                script{                    
                    withSonarQubeEnv(credentialsId: 'saadissad') {
                        sh 'mvn clean package sonar:sonar'

                    }
                        
                }   
                
            }
        }
        stage("Upload war file to nexus"){
            steps{
             
                script{
                    

                    // def readPomVersion = readMavenPom file: 'pom.xml'
                    // def nexusRepo =  readPomVersion.version.endsWith("SNAPSHOT") ? "maven-snapshots" : "maven-release"
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'maven-project', classifier: '', 
                            file: "target/Maven Project.war", type: 'war'
                            ]
                    ],
                    credentialsId: 'nexus3', 
                    groupId: 'com.example.maven-project', 
                    nexusUrl: '13.127.173.152:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "1.0-SNAPSHOT"     
                }               
            }   
                
        }

        
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}
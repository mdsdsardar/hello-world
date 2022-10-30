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
                    

                    def readPomVersion = readMavenPom file: 'pom.xml'
                    def nexusRepo =  readPomVersion.version.endsWith("SNAPSHOT") ? "maven-snapshots" : "maven-release"
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'maven-project', classifier: '', 
                            file: "webapp/target/webapp.war", type: 'war'
                            ]
                    ],
                    credentialsId: 'nexus3', 
                    groupId: 'com.example.maven-project', 
                    nexusUrl: '13.127.173.152:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"     
                }               
            }   
                
        }
        stage("Publish over ssh"){
            tools {
                maven 'Maven'
            }
            steps{ 
                script{
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible', transfers: 
                    [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''cd /opt/docker
                    mkdir $JOB_NAME:v1.$BUILD_ID''', execTimeout: 120000, 
                    flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, 
                    patternSeparator: '[, ]+', remoteDirectory: '//opt//docker', remoteDirectorySDF: false, 
                    removePrefix: 'webapp/target', sourceFiles: 'webapp/target/*.war')], usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, verbose: false), sshPublisherDesc(configName: 'Ansible', transfers: 
                    [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''cd /opt/docker
                    docker build -t $JOB_NAME:v1.$BUILD_ID .
                    docker tag $JOB_NAME:v1.$BUILD_ID mdsdsardar/$JOB_NAME:v1.$BUILD_ID
                    docker tag $JOB_NAME:v1.$BUILD_ID mdsdsardar/$JOB_NAME:latest
                    docker push mdsdsardar/$JOB_NAME:v1.$BUILD_ID
                    docker push mdsdsardar/$JOB_NAME:latest
                    docker rmi $JOB_NAME:v1.$BUILD_ID mdsdsardar/$JOB_NAME:v1.$BUILD_ID mdsdsardar/$JOB_NAME:latest''', 
                    execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, 
                    patternSeparator: '[, ]+', remoteDirectory: '//opt//docker', remoteDirectorySDF: false, removePrefix: '', 
                    sourceFiles: 'Dockerfile')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

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
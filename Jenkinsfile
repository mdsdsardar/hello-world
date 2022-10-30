pipeline{
    agent any
    stages{
        stage("Github automation"){
            steps{
                git branch: 'main', url: 'https://github.com/mdsdsardar/hello-world.git'
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
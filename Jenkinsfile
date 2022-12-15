pipeline{
    
    agent any 
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/avidere/demo-counter-app.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
            stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
                    }
                }
            }
        stage('Upload Artifact to nexus repository'){
            steps{
                script{
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'springboot', 
                        classifier: '', 
                        file: '/workspace/Demo_project/target/springboot-1.0.0.jar', 
                        type: 'jar'
                    ]
                ], 
                    credentialsId: 'nexus', 
                    groupId: 'com.example', 
                    nexusUrl: '172.31.28.226:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'http://35.77.33.50:8081/repository/demoproject/', 
                    version: '1.0.0'
                }
            }
          }
        }
        
}

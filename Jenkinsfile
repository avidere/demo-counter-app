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
                
                   Def mavenpom=readMavenPom 'pom.xml' 
               
                script{
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'springboot', 
                        classifier: '', 
                        file: "target/springboot-${mavenpom.version}.jar", 
                        type: 'jar'
                    ]
                ], 
                    credentialsId: 'nexus', 
                    groupId: 'com.example', 
                    nexusUrl: '172.31.28.226:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'demoproject', 
                    version: "${mavenpom.version}"
                }
            }
          }
        }
        
}

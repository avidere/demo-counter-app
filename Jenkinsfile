pipeline {
    agent any
    environment {
        def git_branch = 'main'
        def git_url = 'https://github.com/avidere/demo-counter-app.git'

        def mvntest = 'mvn test'
        def mvnpackage = 'mvn clean install'
    }
    stages{
        stage{
            steps{
                script{
                    git branch: "${git_branch}", url: "${git_url}"
                }
            }
        }
        stage{
            steps{
                script{
                    sh "${env.mvntest}"
                }
            }
        } 
        stage{
            steps{
                sh "${env.mvnpackage}"
            }
        } 
        stage('Static code analysis') {
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }

            }
        }
        stage('Quality Gate Status') {
                steps{
                    script{
                        waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
                    }
                }
        }
        stage('Upload Artifact to nexus repository') {
            steps {
                script{
                    def mavenpom = readMavenPom file: 'pom.xml'
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
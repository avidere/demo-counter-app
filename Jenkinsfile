/* groovylint-disable-next-line LineLength */
/* groovylint-disable CompileStatic, DuplicateStringLiteral, NestedBlockDepth, UnusedVariable, VariableName, VariableTypeRequired */
pipeline {
    agent any
    environment {
        def git_branch = 'main'
        def git_url = 'https://github.com/avidere/demo-counter-app.git'

        def mvntest = 'mvn test '
        def mvnpackage = 'mvn clean install'

        def sonar_cred = 'sonar'
        def code_analysis = 'mvn clean install sonar:sonar'
        def utest_url = 'target/surefire-reports/**/*.xml'

        def mavenpom = readMavenPom file: 'pom.xml'
    }
    stages {
        stage('Git Checkout') {
            steps {
                script {
                    git branch: "${git_branch}", url: "${git_url}"
                    echo 'Git Checkout Completed'
                }
            }
        }
        stage('Maven Build') {
            steps {
                sh "${env.mvnpackage}"
                echo 'Maven Build Completed'
            }
        }
        stage('Unit Testing and publishing reports') {
            steps {
                script {
                    sh "${env.mvntest}"
                    echo 'Unit Testing Completed'
                }
            }
            post {
                success {
                        junit "$utest_url"
                        jacoco()
                }
            }
        }
        stage('Static code analysis and Quality Gate Status') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: "${sonar_cred}") {
                        sh "${code_analysis}"
                    }
                    waitForQualityGate abortPipeline: true, credentialsId: "${sonar_cred}"
                }
            }
        }
        stage('Upload Artifact to nexus repository') {
            steps {
                script {
                    /* groovylint-disable-next-line LineLength */

                    def nex_repo = env.mavenpom.version.endsWith('SNAPSHOT') ? 'demoproject-snapshot' : 'demoproject-Release'
                    def nex_cred = 'nexus'
                    def grp_ID = 'com.example'
                    def nex_url = '172.31.28.226:8081'
                    def nex_ver = 'nexus3'
                    def proto = 'http'
                    nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'springboot',
                        classifier: '',
                        file: "target/springboot-${env.mavenpom.version}.jar",
                        type: 'jar'
                    ]
                ],
                    credentialsId: "${nex_cred}",
                    groupId: "${grp_ID}",
                    nexusUrl: "${nex_url}",
                    nexusVersion: "${nex_ver}",
                    protocol: "${proto}",
                    repository: "${nex_repo}",
                    version: "${env.mavenpom.version}"
                    echo 'Artifact uploaded to nexus repository'
                }
            }
        }
    }
}


pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/GhazouaniHichem/Java-CI-CD-complete-pipeline.git'
            }
        }
        
        stage('Code Compile') {
            steps {
                    sh "mvn compile"
            }
        }
        
        stage('Run Test Cases') {
            steps {
                    sh "mvn test"
            }
        }
        
        stage('Static Code Analysis') {
            environment {
                SONAR_URL = "http://13.38.76.193:9000/"
            }
            steps {
                withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_AUTH_TOKEN')]) {
                sh 'cd spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
                }
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                   dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
                   dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Maven Build') {
            steps {
                    sh "mvn clean compile"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                   script {
                       withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t webapp ."
                            sh "docker tag webapp ghazouanihm/webapp:latest"
                            sh "docker push ghazouanihm/webapp:latest "
                        }
                   } 
            }
        }
        
        stage('Docker Image scan') {
            steps {
                    sh "trivy image ghazouanihm/webapp:latest "
            }
        }
        
    }
}

pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git CheckOut') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/sachi3050/Multi-Tier-With-Database.git'
            }
        }
        stage('Source Code Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Maven Test Cases') {
            steps {
                sh 'mvn test'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQUBE') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=GoldenCat-BANK \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=GoldenCat-BANK '''
                }
            }
        }
        stage('Maven Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Docker Image Build and Tag') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'Docker-Cred') {
                        sh 'docker build -t gc-bank .'
                        sh 'docker tag gc-bank sachidananda06/gc-bank:money1'
                    }
                }
            }
        }
        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image sachidananda06/gc-bank:money1'
            }
        }
        stage('Docker Push Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'Docker-Cred') {
                        sh 'docker push sachidananda06/gc-bank:money1'
                    }
                }
            }
        }
        stage('Docker Deploy') {
            steps {
                script {
                    sh 'docker run -d --name GC-Bank123 -p 8087:8087 sachidananda06/gc-bank:money1'
                }
            }
        }
        
    }
}

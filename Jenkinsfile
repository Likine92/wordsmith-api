pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Build') {
            tools{
                jdk 'jdk'
            }
            steps {
                sh 'java --version'
                sh 'mvn clean install'
            }
        }
        
        stage('Unit test') {
            tools{
                jdk 'jdk'
            }
            steps {
                sh 'mvn test'
            }
        }
        stage('Sonar Scan') {
            steps {
                withSonarQubeEnv("sonar"){
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar'
                }
            }
        }

        stage("Quality Gate"){
            steps{
                script{
                    timeout(time: 1, unit: 'HOURS') { 
                        def qg = waitForQualityGate() 
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }

        stage("Build Docker Image"){
            steps{
                script{
                    dir("${WORKSPACE}"){
                        sh "ls -l"
                        sh"docker build -t 174447486748.dkr.ecr.us-east-1.amazonaws.com/wordsmith-api:1.0-SNAPSHOT -f main_Dockerfile . "
                    }
                }
            }
        }

        stage("Push To Ecr"){
            steps{
                script{
                    sh"aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 174447486748.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker push 174447486748.dkr.ecr.us-east-1.amazonaws.com/wordsmith-api:1.0-SNAPSHOT"
                }
            }
        }
    }
}

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

    }
}

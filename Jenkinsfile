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

    }
}

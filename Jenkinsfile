pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Build') {
            steps {
                sh 'java --version'
                sh 'mvn clean install'
            }
        }
        
        stage('Unit test') {
            steps {
                sh 'mvn test'
            }
        }

    }
}

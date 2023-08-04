pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'jdk'
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

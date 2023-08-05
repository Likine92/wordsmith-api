def tag
def name = "wordsmith-api"
def registry = "174447486748.dkr.ecr.us-east-1.amazonaws.com"
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
                script{
                    tag = getComponentTag()
                    sh 'mvn clean install'
                }
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
                        sh"docker build -t ${registry}/${name}:${tag}  . "
                    }
                }
            }
        }

        stage("Push To Ecr"){
            steps{
                script{
                    withAWS([credentials:'aws-creds',region:'us-east-1']){
                        sh"aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${registry}"
                        sh "docker push ${registry}/${name}:${tag}"
                    }
                }
            }
        }
    } 
    post{
        failure{
                withAWS([credentials:'aws-creds',region:'us-east-1']){
                    sh"aws sns publish --topic-arn arn:aws:sns:us-east-1:174447486748:jenkins-notification --message 'Build failed for component ${name} Build URl: ${BUILD_URL}' --subject 'Build Status'"
                }
            }
    }
}

void getComponentTag(){
    def pom = readMavenPom file: 'pom.xml'
        version = pom.version
        println version
    def branch = "${BRANCH_NAME}"
        println branch
        branch = branch.replaceAll("/","-")
        println branch
    def buildNumber = "${BUILD_NUMBER}"
        println buildNumber
    def tag
        if(branch == "develop"){
            tag = "${version}-rc-${buildNumber}"
        } else if(branch == "main"){
            tag = "${version}-${buildNumber}"
        } else{
            tag = "${version}-${branch}.${buildNumber}"
        }
        return tag
}    
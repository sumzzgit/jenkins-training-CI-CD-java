pipeline {
    agent any
    tools{
        maven 'maven'
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: 'master' , url:'https://github.com/sumzzgit/jenkins-training-CI-CD-java.git'
            }
        }
        stage('build') {
            steps {
                withSonarQubeEnv('sonar'){
                    withMaven(maven:'maven'){
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage('Qaulity Gate'){
            steps{
                timeout(time: 5, unit: 'MINUTES'){
                waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('deploy'){
            steps{
                sh 'sudo mv /var/lib/jenkins/workspace/usecase_test/target/*.war /home/ec2-user/test/'
                //sh 'sudo docker rmi tomcat:custom'
                sh 'sudo docker build -t tomcat:custom -f /home/ec2-user/test/Dockerfile .'
                sh 'sudo docker run -d -p 8085:8080 --name tomcat tomcat:custom'
            }
        }
    }
}

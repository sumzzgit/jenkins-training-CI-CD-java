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
      /*  stage('Qaulity Gate'){
            steps{
                timeout(time: 5, unit: 'MINUTES'){
                waitForQualityGate abortPipeline: true
                }
            }
        }*/
        stage('pre-deploy'){
            steps{
                sh 'sudo rm -f /home/sumzz/test/*.war'
                sh 'sudo sleep 10'
                sh 'sudo mv /var/lib/jenkins/workspace/dev-test/target/*.war /home/sumzz/test/'
                sh 'sudo sleep 10'
                //sh 'sudo docker rmi -f tomcat:custom'
            }
        }
        stage('deploy'){
            steps{
                sh 'sudo docker build -t tomcat:custom -f /home/sumzz/test/Dockerfile .'
                sh 'sudo sleep 10'
                sh 'sudo docker run -d -p 8085:8080 --name tomcat tomcat:custom'
                sh 'sudo docker cp /home/sumzz/test/*.war tomcat:/usr/local/tomcat/webapps/'
            }
        }
    }
}

pipeline{
    agent any
    stages{
        stage('git'){
            steps{
                git branch: 'master', url: 'https://github.com/sumzzgit/jenkins-training-CI-CD-java'
            }
        }
        stage('build'){
            steps{
                sh ''' export MAVEN_HOME=/usr/bin/mvn
                       mvn clean verify
                '''
            }
        }
        stage('copy war file'){
            steps{
                sh 'sudo cp /var/lib/jenkins/workspace/testtomcat1/target/*.war /home/ec2-user/jenkins/testjava1/'
            }
        }
        stage('dockerfile'){
            steps{
                sh ''' sudo /home/ec2-user/jenkins/testjava1/tomcat_dockerfile.sh
                '''
            }
        }
        stage('build image'){
            steps{
                sh '''cd /home/ec2-user/jenkins/testjava1/
                      sudo docker build -t tomcat_image -f tomcat.Dockerfile .
                '''
            }
        }
        stage('deploy'){
            steps{
                sh '''sudo docker run -d --name tomcat -p 8081:8080 tomcat_image
                '''
            }
        }
    }
}

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
    }
}

pipeline {
    agent any
    tools{
        maven 'maven'
    }
    environment {
   NEXUS_VERSION = "nexus3"
   NEXUS_PROTOCOL = "http"
   NEXUS_URL = "65.2.177.235:8081"
   NEXUS_REPOSITORY = "project-1-maven-repo"
   NEXUS_CREDENTIAL_ID = "nexus-cred"
}
    options{
        copyArtifactPermission('nexus-fetch');
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
        stage("Publish to Nexus Repository Manager"){
            steps{
                script{
                     pom = readMavenPom file: "pom.xml";
                     filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                     echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                     artifactPath = filesByGlob[0].path;
                     artifactExists = fileExists artifactPath;
                     if(artifactExists){
                         echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                         nexusArtifactUploader(
                              nexusVersion: NEXUS_VERSION,
                              protocol: NEXUS_PROTOCOL,
                              nexusUrl: NEXUS_URL,
                              groupId: pom.groupId,
                              version: pom.version,
                              repository: NEXUS_REPOSITORY,
                              credentialsId: NEXUS_CREDENTIAL_ID,
                              artifacts:[
                                  [
                                      artifactId: pom.artifactId,
                                      classifier: '',
                                      file: artifactPath,
                                      type: pom.packaging
                                      ],
                                      [
                                          artifactId: pom.artifactId,
                                          classifier: '',
                                          file: "pom.xml",
                                          type: "pom"
                                          ]
                                  ]
                             );
                     }else{
                         error "*** File: ${artifactPath}, could not be found";
                     }
                      
                }
            }
        } 
        /*stage("archive artifact"){
            steps{
                script{
                    pomFile = readMavenPom file: "pom.xml";
                    archiveArtifacts artifacts: 'pom.xml'
                    build job: 'nexus-fetch' , parameters: [
                        string(name: 'artifactId', value: "${pomFile.artifactId}"),
                        string(name: 'version', value: "${pomFile.version}"),
                        string(name: 'build_number', value: "${currentBuild.number}"),
                        string(name: 'job_name', value: "${JOB_NAME}")
                        ]
                }
            }
        }*/
    }
}

pipeline {
    agent any
    
    tools
    {
       maven "Maven"
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.31.34.169:8081"
        NEXUS_REPOSITORY = "MyApp"
        NEXUS_CREDENTIAL_ID = "Nexus"
    }
     
    stages {
      stage(' SCM checkout') {
           steps {
             
                git branch: 'Demo', url: 'https://github.com/sujatabehera/Ansible-Jenkins-Demo.git'
             
          }
        }
         
      
      stage('Build and package war file') {
           steps {
             
                sh 'mvn deploy -s settings.xml'             
          }
        }
        stage("Publish war file to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
        
         
        
        
        
        stage('Deploy War file from Nexus to tomcat server using ansible') {
             
            steps {
                 
             
               
               // sh "ansible-playbook main.yml -i inventories/dev/hosts --user devops --key-file Demo.pem"
               ansiblePlaybook credentialsId: 'Private-key', disableHostKeyChecking: true, installation: 'Ansible', extras: "-e JENKINS_BUILD_NUMBER=$BUILD_NUMBER", inventory: 'dev.inv', playbook: 'main.yml'

               
            
            }
        }
    }
}

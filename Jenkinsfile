pipeline {
    agent any
    
    tools
    {
       maven "Maven"
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "3.110.148.207:8081"
        NEXUS_REPOSITORY = "MyApp"
        NEXUS_CREDENTIAL_ID = "Nexus"
    }
     
    stages {
      stage(' SCM checkout') {
           steps {
             
                git branch: 'Demo', url: 'https://github.com/sujatabehera/Ansible-Jenkins-Demo.git'
             
          }
        }
         
      stage("Sonar Analysis") {
             steps {
                script {
            withSonarQubeEnv('SonarQube') {
        sh """/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonarqube/bin/sonar-scanner \
     -D sonar.projectVersion=1.0-SNAPSHOT \
     -D sonar.login=admin \
      -D sonar.password=admin \
      -D sonar.projectBaseDir=/var/lib/jenkins/workspace/Sonar-Jenkins-Demo/ \
        -D sonar.projectKey=project \
        -D sonar.sourceEncoding=UTF-8 \
        -D sonar.language=java \
        -D sonar.tests=src/main \
        -D sonar.host.url=http://13.127.148.200:9000/"""
    sh """ mvn sonar:sonar \
  -Dsonar.projectKey=project \
  -Dsonar.host.url=http://ec2-3-109-186-92.ap-south-1.compute.amazonaws.com:9000 \
  -Dsonar.login=241b9c6a8fb9e7ce2f06c6b6fe7816d62b9629b6 """
            }
                    
                    }
        }

        }
        
         stage('Build and package war file') {
           steps {
             
                sh 'mvn package -DskipTests=true -DbumpPatch'             
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
               ansiblePlaybook credentialsId: 'Private-key', disableHostKeyChecking: true, installation: 'Ansible', inventory: 'dev.inv', playbook: 'main.yml'

               
            
            }
        }
    }
}

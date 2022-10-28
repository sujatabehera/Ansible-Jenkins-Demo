pipeline {
    agent any
    
    tools
    {
       maven "Maven"
    }
     
    stages {
      stage(' SCM checkout') {
           steps {
             
                git branch: 'master', url: 'https://github.com/sujatabehera/Ansible-Jenkins-Demo.git'
             
          }
        }
         
     
        
         stage('Build') {
           steps {
             
                sh 'mvn package'             
          }
        }
        
        
         
        
        
        
        stage('Deploy War file to tomcat server using ansible') {
             
            steps {
                 
             
               
               // sh "ansible-playbook main.yml -i inventories/dev/hosts --user devops --key-file Demo.pem"
               ansiblePlaybook credentialsId: 'Private-key', disableHostKeyChecking: true, installation: 'Ansible', inventory: 'dev.inv', playbook: 'main.yml'

               
            
            }
        }
    }
}

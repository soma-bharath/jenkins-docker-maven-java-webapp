pipeline {
    
    agent {
        label "linuxnode"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/soma-bharath/jenkins-docker-maven-java-webapp.git'
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t  somabharathkumar/javaweb:${BUILD_TAG}  ."
                //sh 'whoami'
            }
            
        }
        
        
        stage('Push Image to Docker HUB') {
            steps {
                
                withCredentials([string(credentialsId: 'dockerpassword', variable: 'dockerpass')]) {
    // some block
                 sh "sudo docker login -u somabharathkumar -p $dockerpass"
}
               
               sh "sudo docker push somabharathkumar/javaweb:${BUILD_TAG}"
            }
            
        }
        
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run  -d  -p  8080:8080 --name myjavaapp   somabharathkumar/javaweb:${BUILD_TAG}"
                //sh 'whoami'
            }
            
        }
        
        
        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['qamachine']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@18.207.206.18 sudo docker rm -f myjavaapp"
                    sh "ssh ec2-user@18.207.206.18 sudo docker run  -d  -p  8080:8080 --name myjavaapp   somabharathkumar/javaweb:${BUILD_TAG}"
                }

            }
            
        }
        
        
         stage('QAT Test') {
            steps {
                
               // sh 'curl --silent http://13.233.100.238:8080/java-web-app/ |  grep India'
                
                retry(10) {
                    sh 'curl --silent http://13.233.100.238:8080/java-web-app/ |  grep India'
                }
            
               
            }
        }
          
        
         
         
        stage('approved') {
            steps {
                
            
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
        
        
         
        
        stage('Deploy webAPP in Prod Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
    
                    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.232.250.244 sudo kubectl  delete    deployment myjavawebapp"
                    sh "ssh  ec2-user@13.232.250.244 sudo kubectl  create    deployment myjavawebapp  --image=vimal13/javaweb:${BUILD_TAG}"
                    sh "ssh ec2-user@13.232.250.244 sudo wget https://raw.githubusercontent.com/vimallinuxworld13/jenkins-docker-maven-java-webapp/master/webappsvc.yml"
                    sh "ssh ec2-user@13.232.250.244 sudo kubectl  apply -f webappsvc.yml"
                    sh "ssh ec2-user@13.232.250.244 sudo kubectl  scale deployment myjavawebapp --replicas=5"
                }

            }
            
        } 
        
    
        
    }
    
  
        
     post {
         always {
             echo "You can always see me"
         }
         success {
              echo "I am running because the job ran successfully"
         }
         unstable {
              echo "Gear up ! The build is unstable. Try fix it"
         }
         failure {
             echo "OMG ! The build failed"
             mail bcc: '', body: 'hi check this ..', cc: '', from: '', replyTo: '', subject: 'job ete fail', to: 'vdaga@lwindia.com'
         }
     }

    
    
}

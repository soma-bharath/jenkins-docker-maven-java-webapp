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
        
        
         
    
    
}

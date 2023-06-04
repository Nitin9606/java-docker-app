pipeline {
    
    agent any 
    stages {
        stage('SCM') {
	    agent {
	    	label "pipeline"
		}
            steps {
                git 'https://github.com/gouravaas/pipeline_code.git'
            }
            
        }
        
        stage('Build by Maven Package') {
	    agent {    
		label "pipeline"    
		}
            steps {
                sh 'mvn clean package'
            }
            
        }
        /*stage('Deploy on testing') {
	    agent {    
		label "pipeline"    
		}
	    
            steps {
		// sh 'pwd'
		 sh "sudo docker build -t Gouravaas/javatest-app:${BUILD_TAG} ."
		}
	} */       
        /*stage('Pushing image to docker hub') {
	    agent {    
		label "pipeline"    
		}
            steps {
	        withCredentials([string(credentialsId: 'docker_hub_passwd', variable: 'Docker_hub_passwd_var')]) {

		sh "sudo docker login -u Gouravaas -p ${docker_hub_passwd_var}"
		}
		//sh "sudo docker push Gouravaas/javatest-app:${BUILD_TAG}"
	}        
   
      }*/
      stage('Deploy on Docker Container') {
      	agent {
		label "pipeline"    
		}
	steps {
		sh "sudo docker rm -f web1"
		//sh "sudo docker rmi ${docker images -a q}"
		// sh "sudo docker pull Gouravaas/javatest-app:jenkins-pipeline-code-17"
		sh "sudo docker run -dit --name web1 -p 49153:8080 Gouravaas/javatest-app:jenkins-pipeline-code-17"
		}

	}
	stage('QAT testing') {
	agent { 
		label "pipeline"
		}
	steps {
		retry(5) {
		sh "curl --silent http://35.154.193.35:49153/java-web-app/ | grep -i zeetron"
		}
	   }
	}

	stage("Approval status") {
		agent {
			label "pipeline"
			}
		steps {
		script {  
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput
			}
	 	}
	}
	stage('Deploy webAPP in QA/Test Env') {
      	    agent {
		label "pipeline"    
		}
            steps {
               
               sshagent(['ec2-access-access-cred']) {
    
                    //sh "ssh  -o  StrictHostKeyChecking=no ec2-user@15.207.106.62 sudo docker rm -f web1"
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@15.207.106.62 sudo docker run  -d  -p  49153:8080 --name web1 Gouravaas/javatest-app:jenkins-pipeline-code-17"
		         }
		}
   	}
    }
}


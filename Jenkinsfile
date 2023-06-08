pipeline{
	
	agent {
		label "ubuntu_slave"
		}
		stages{
			stage ("Pull the code from SCM"){
				steps {
					git branch: 'main', url: 'https://github.com/Nitin9606/java-docker-app.git'
					}
				}
			stage (" Build the code "){
				steps {
					sh 'sudo mvn dependency:purge-local-repository'
					sh 'sudo mvn clean package'
					}
				}
			stage (" Build the image "){
				steps {
					sh 'sudo docker build -t java-repo:$BUILD_TAG .'
					sh 'sudo docker tag java-repo:$BUILD_TAG nitinbijlwan/pipeline-java:$BUILD_TAG'
					}
				}
			stage ( " push the image "){
				steps {
				withCredentials([string(credentialsId: 'docker_hub_passwd', variable: 'docker_hub_password_var')]){
					sh 'sudo docker login -u nitinbijlwan -p $docker_hub_password_var'
					sh 'sudo docker push nitinbijlwan/pipeline-java:$BUILD_TAG'
						}
					}
				}
			stage("QAT Testing") {
				steps {
					script {
						sh 'sudo docker run -dit -p 8000:8080 nitinbijlwan/pipeline-java:$BUILD_TAG'
						}
					}
				}
			stage("testing complete") {
				steps {
					retry(5) {
						script {
							sh 'echo "This project is complete"'
							}
						}
					}
				}
			stage("Approval status") {
				steps {
					script {
						 Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                				echo 'userInput: ' + userInput
					}
				}
			}
			stage("Prod Env") {
				steps {
					sshagent(['ubuntu']) {
			    	 	sh "ssh -o StrictHostKeyChecking=no ubuntu@13.233.83.242 sudo docker run  -d  -p  49153:8080  gouravaas/java-app:$BUILD_TAG"
				}
			}
		}
	}
		
}

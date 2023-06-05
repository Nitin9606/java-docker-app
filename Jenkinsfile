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
			}
		
}

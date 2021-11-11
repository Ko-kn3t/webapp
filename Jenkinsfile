pipeline{

	agent any

	tools{
		maven 'Maven'
	}

	stages {
		stage ('Initialize') {
			steps {
				sh '''
					echo "PATH=${PATH}"
					echo "M2_HOME=${M2_HOME}"
				'''
			}
		}

		stage ('Check-Git-Secrets') {
			steps {
				sh 'rm trufflehog || true'
				sh ' sudo su -c "whoami" -s /bin/sh ubuntu > me'
				sh 'cat me'
			}
		}

		stage ('Build') {
			steps {
				sh 'mvn clean package'
			}
		}

		stage ('Deploy-To-Tomcat') {
			steps {
				sshagent(['tomcat']) {
					sh 'scp -o StrictHostkeyChecking=no target/*.war ubuntu@3.0.55.205:/home/ubuntu/prod/apache-tomcat-8.5.72/webapps/webapp.war'
				}
			}
		}
	}
}





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
				sh 'docker run dxa4481/trufflehog --json https://github.com/Ko-kn3t/webapp.git > trufflehog'
				sh 'cat trufflehog'
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





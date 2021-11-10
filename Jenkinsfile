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

		stage ('Build') {
			steps {
				sh 'mvn clean package'
			}
		}

		stage ('Deploy-To-Tomcat') {
			steps {
				sshagent(['tomcat']) {
					sh 'scp -o StrictHostkeyChecking=no target/*.war ubuntu@st-1.compute.amazonaws.com	3.0.55.205:/tmp//home/ubuntu/prod/apache-tomcat-8.5.72/webapps/webapp.war'
				}
			}
		}
	}
}





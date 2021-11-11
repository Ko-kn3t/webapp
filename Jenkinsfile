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
				sh 'sudo su -c "docker run gesellix/trufflehog --json https://github.com/Ko-kn3t/webapp.git" -s /bin/sh jenkins > trufflehog'
				sh 'cat trufflehog'
			}
		}
	

		stage ('Source Composition Analysis') {
			steps {
				sh 'rm owasp* || true'
				sh 'wget https://raw.githubusercontent.com/Ko-kn3t/webapp/master/owasp-dependency-check.sh'
				sh 'chmod +x owasp-dependency-check.sh'
				sh 'bash owasp-dependency-check.sh'
			}
		}
		
		stage('SAST') {
			steps {
				withSonarQubeEnv('sonar') {
					sh 'mvn sonar:sonar'
					sh 'cat target/sonar/report-task.txt'
				}
			}
		}
		
		stage("Quality Gate") {
			steps {
				timeout(time: 10, unit: 'MINUTES'){
				waitForQualityGate abortPipeline: true
				}
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





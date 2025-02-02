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
		
		stage('DAST - ZAP') {
			steps {
				sshagent(['zap']) {
					sh 'ssh -o StrictHostkeyChecking=no ubuntu@3.1.101.184 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://3.0.55.205:8080/webapp" || true'
				}
			}
		}

		stage('DAST - Nuclei') {
			steps {
				sshagent(['nuclei']) {
					sh 'ssh -o StrictHostkeyChecking=no ubuntu@54.169.196.134 "nuclei -u http://3.0.55.205:8080/webapp -t  /home/ubuntu/nuclei-templates" || true'
				}
			}
		}
	}
}





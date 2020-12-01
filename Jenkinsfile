pipeline {
	agent any
	stages {
		stage('OWASP DependencyCheck') {
			steps {
				dependencyCheck additionalArguments: '--format HTML --format XML', odcInstallation: 'Default'
			}
			post {
				always{
					dependencyCheckPublisher pattern: 'dependency-check-report.xml'
				}
			}
		}
		
		stage('Integration UI Test') {
			parallel {
				stage('Headless Browser Test') {
					agent any
					steps {
						sh 'mvn -B -DskipTests clean package'
						sh 'mvn test'
					}
					post {
						always {
							junit 'target/surefire-reports/*.xml'
						}
					}
				}
			}
		}
		stage('Test') {
			steps {
                sh 'phpunit --log-junit logs/unitreport.xml -c tests/phpunit.xml tests'
            }
			post {
				always{
					junit testResults: 'logs/unitreport.xml'
				}
			}
		}
	}	
	
}

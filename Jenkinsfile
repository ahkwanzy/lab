pipeline {
    agent any
    stages {
        /**
        stage('OWASP DependencyCheck') {
            steps {
                dependencyCheck additionalArguments: '--format HTML --format XML', odcInstallation: 'OWASP Dependency-Check'
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
        **/
        stage ('Checkout')  {
            steps {
                git branch:'master', url: 'https://github.com/ScaleSec/vulnado.git'
            }
        }

        stage ('Build') {
            steps {
                sh 'mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile=false -Dmaven.test.failure.ignore'
            }
        }

        stage ('Analysis') {
            steps {
                sh 'mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs'
            }
        }

        post {
            always {
                junit testResults: '**/target/surefire-reports/TEST-*.xml'
                recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
                recordIssues enabledForFailure: true, tool: checkStyle()
                recordIssues enabledForFailure: true, tool: spotBugs(pattern: '**/target/findbugsXml.xml')
                recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml')
                recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
            }
        }
    }
}

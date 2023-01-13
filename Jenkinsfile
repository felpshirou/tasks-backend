pipeline{
	agent any
	stages{
		stage ('Build Backend'){
		steps{
			bat 'mvn clean package -DskipTest=true'
			}
		}
		stage ('Unit Tests'){
		steps{
			bat 'mvn test'
			}
		}
		stage ('Sonar Analysis'){
		environment{
			scannerHome = tool 'SONAR_SCANNER'
			}
		steps{
			withSonarQubeEnv('SONAR_LOCAL'){
				bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=a95f6ad48d7656e2d36805c6eded3137bb8cedda -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
				}
			
			}
		}
		stage ('Quality Gate'){
			steps{
				sleep(5) 
				timeout(time: 1, unit: 'MINUTES'){
				waitForQualityGate abortPipeline: true
				}				
			}
		}
		stage ('Deploy Backend'){
			steps{
				deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
				}
		}
		stage ('Api Test'){
			steps{
					git credentialsId: '6cd55045-240e-41cc-b9c2-6859361c2b09', url: 'https://github.com/felpshirou/tasks-api-test'
					bat 'mvn test'
				
				}
		}
		stage ('Deploy Frontend'){
			steps{
				dir('frontend'){
				git credentialsId: '6cd55045-240e-41cc-b9c2-6859361c2b09', url: 'https://github.com/felpshirou/tasks-frontend'
				bat 'mvn clean package'
				deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks', war: 'target/tasks.war'
					}
				}
		}
		stage ('Functional Test'){
			steps{
					dir('functional-test'){
					git credentialsId: '6cd55045-240e-41cc-b9c2-6859361c2b09', url: 'https://github.com/felpshirou/tasks-functional-tests'
					bat 'mvn test'
						}
				
				}
		}
	}
}
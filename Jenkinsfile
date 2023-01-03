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
			WithSonarQubeEnv('SONAR_LOCAL'){
				bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=a95f6ad48d7656e2d36805c6eded3137bb8cedda -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/** , **/src/test/**, **/model/**, **Application.java"
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
				deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
				}
		}
		stage ('Api Test'){
			steps{
				dir('api-test') {
				git 'https://github.com/felpshirou/tasks-api-test'
				bat 'mvn test'
				}
			}
		}
	}
}

@Library('SharedLibKKFunda') _
pipeline {

    agent any

    tools {
        maven "Maven_3.9.9"
    }

    stages {
        
        stage('Set Build Name') {
            steps {
                script {
                    currentBuild.displayName = "Airtel-qa-Release-${env.BUILD_NUMBER}"
                    currentBuild.description = "CI/CD Pipeline for Airtel-Dev"
                }
            }
        }
        
    
        
        stage('Git Checkout') {
            steps {
                git branch: 'qa', url: 'https://github.com/naniawsk8s-sudo/maven-webapplication-project-kkfunda.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('Build') {
            steps {
                sh "mvn package"
            }
        }

        stage('SonarQube Report') {
            steps {
                sh "mvn sonar:sonar"
            }
        }

        stage('Deploy to Nexus') {
            steps {
                sh "mvn deploy"
            }
        }

      	stage('deploy to tomcat')
		{
			steps{
				deploy adapters: [
					tomcat9(
							credentialsId: 'tomcat',
							url: 'http://13.126.65.220:8080'
						)
				],
				contextPath:'/maven-web-application', // target path 
				war: 'target/*.war' // source path 
				
				}
		}
		stage('downstream-Airtel-prod')
		{
			steps{
				build job: 'airtel-Prod' //  This is downstream
			}
		}
		
    }

    post {
        success {
            script {
                sendSlackNotifications(currentBuild.result)
            }
        }

        failure {
            script {
                sendSlackNotifications(currentBuild.result)
            }
        }
    }
}

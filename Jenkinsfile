// parallel jobs
pipeline {

    agent any  // This means the pipeline can run on any available Jenkins agent

    tools {
        maven 'maven 3.9.9'   // Use Maven 3.9.9 tool configured in Jenkins
    }

    stages {

        // Stage 1: Checkout the code from GitHub
        stage('Checkout') {
            steps {
				 git branch: 'f9', url: 'https://github.com/naniawsk8s-sudo/maven-webapplication-project-kkfunda.git'
			   }
        }


        // Stage 2: Maven Build, Sonar scan, and Nexus deploy (Run in parallel)
        stage('maven and sonar') {
            steps {
                parallel (
                    "Build": {
                        sh "mvn clean package"  // Run Maven build
                    },
                    "Sonar": {
                        sh "mvn sonar:sonar"  // Run Sonar scan

                    }
                )
            }
        }
		
		// Stage 3 : Tomcat and nexus deploymebn 
		stage('Nexus and Tomcat')
			steps{
				parallel (
					"Build": {
						sh "mvn deploy" // nexus deploy
					},
					"Tomcat": {
								deploy adapters: [
									tomcat9(
									credentialsId: 'tomcat',
									url: 'http://13.126.65.220:8080'
									)
								],
								contextPath:'/maven-web-application', // target path 
								war: 'target/*.war' // source path 	
							}
						)
				}
		
		
    }
}

pipeline {

    agent any

    tools {
        maven 'Maven_3.9.9'
    }

    stages {

        // Checkout
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/naniawsk8s-sudo/maven-webapplication-project-kkfunda.git'
            }
        }

        // Build and Sonar
        stage('Maven and Sonar') {
            steps {
                script {
                    parallel(
                        "Build": {
                            sh "mvn clean package"
                        },
                        "Sonar": {
                            sh "mvn sonar:sonar"
                        }
                    )
                }
            }
        }

        // Nexus and Tomcat
        stage('Nexus and Tomcat') {
            steps {
                script {
                    parallel(
                        "Nexus": {
                            sh "mvn deploy"
                        },
                        "Tomcat": {
                            deploy adapters: [
                                tomcat9(
                                    credentialsId: 'tomcat',
                                    url: 'http://13.126.65.220:8080'
                                )
                            ],
                            contextPath: '/maven-web-application',
                            war: 'target/*.war'
                        }
                    )
                }
            }
        }
    }
}

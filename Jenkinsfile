
node {

    def mavenHome = tool name: "Maven_3.9.9"
	echo "git branch name: ${env.NRANCH_NAME}"
	echo "build number: ${env.BUILD_NUMBER}"

    try {

        stage('Checkout') {
            git branch: 'dev',
                url: 'https://github.com/naniawsk8s-sudo/maven-webapplication-project-kkfunda.git'
        }

        stage('Compile') {
            sh "${mavenHome}/bin/mvn compile"
        }

        stage('Build') {
            sh "${mavenHome}/bin/mvn clean package"
        }

        stage('SonarQube Report') {
            sh "${mavenHome}/bin/mvn sonar:sonar"
        }

        stage('Deploy to Nexus') {
            sh "${mavenHome}/bin/mvn deploy"
        }

        stage('Deploy to Tomcat') {
            deploy adapters: [
                tomcat9(
                    credentialsId: 'tomcat',
                    url: 'http://13.126.65.220:8080'
                )
            ],
            contextPath: '/maven-web-application',
            war: 'target/*.war'
        }

        // Success Notification
        slackSend(
            channel: '#all-newtonjenkinsjobs',
            color: 'good',
            message: """
✅ *Jenkins Build Successful*

*Job:* ${env.JOB_NAME}
*Build:* #${env.BUILD_NUMBER}
*Status:* SUCCESS
*Branch:* dev
*Build URL:* ${env.BUILD_URL}
"""
        )

    } //try block end 

catch (Exception e) {

        currentBuild.result = 'FAILURE'

        // Failure Notification
        slackSend(
            channel: '#all-newtonjenkinsjobs',
            color: 'danger',
            message: """
❌ *Jenkins Build Failed*

*Job:* ${env.JOB_NAME}
*Build:* #${env.BUILD_NUMBER}
*Status:* FAILURE
*Branch:* dev
*Build URL:* ${env.BUILD_URL}
"""
        )

        throw e

    } 

finally {

        // Final Notification
        slackSend(
            channel: '#all-newtonjenkinsjobs',
            color: '#439FE0',
            message: """
📢 *Pipeline Finished*

*Job:* ${env.JOB_NAME}
*Build:* #${env.BUILD_NUMBER}
*Final Status:* ${currentBuild.currentResult}
*Build URL:* ${env.BUILD_URL}
"""
        )
    }
}

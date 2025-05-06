node
{

	echo "Git branch name is: ${env.BRANCH_NAME}"
	echo "Build number is: ${env.BUILD_NUMBER}" 
	echo "Build name is: ${env.JOB_NAME}"

	def mavenHome=tool name: "maven-3.9.9"

	

	stage('Git integration')
	{
		git branch: 'prod', credentialsId: 'github-pat', url: 'https://github.com/sa5i/maven-web-app-project-kk-funda.git'
	}

	stage('Compile')
	{
	sh "${mavenHome}/bin/mvn clean compile"
	}

	stage('Maven Build')
	{
	sh "${mavenHome}/bin/mvn clean package"
	}

	stage('SonarQ')
	{
	sh "${mavenHome}/bin/mvn clean sonar:sonar"
	}
	 stage('Deploy to Nexus')
    {
        sh "${mavenHome}/bin/mvn deploy"
    }
    stage('Deploy to tomcat')
    {
        sh "scp -i /home/sa5i/.ssh/id_rsa target/maven-web-app-project-kk-funda.war sa5i@h13.49.80.41/opt/tomcat/webapps/"
    }

}

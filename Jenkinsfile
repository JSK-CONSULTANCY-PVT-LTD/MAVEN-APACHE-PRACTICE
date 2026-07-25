node 
{
    //   /var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/Maven
   def mavenHome=tool name: "Maven"
   echo "git branch Name: ${env.BRANCH_NAME}"
   echo "build number: ${env.BUILD_NUMBER}"

   try
   {  

   stage('Git Checkout')
   {
    notifyBuild('STARTED')
    git branch: 'Dev', url: 'https://github.com/JSK-CONSULTANCY-PVT-LTD/MAVEN-APACHE-PRACTICE.git'
   }
   stage('Compile')
   {
    sh "${mavenHome}/bin/mvn compile"
   }

   stage('Build')
   {
    sh "${mavenHome}/bin/mvn clean package"

   }
   stage('Sonar Qube Report')
   {
    sh "${mavenHome}/bin/mvn sonar:sonar"
   }

   stage('Deploy Into Nexus')
   {
    sh "${mavenHome}/bin/mvn clean deploy"
   }

    stage('Deploy Into Tomcat') 
    {
      
      sh """

      curl -u sai:password \
--upload-file /var/lib/jenkins/workspace/Automation-pipeline-script/target/maven-web-application.war \
"http://3.110.119.128:8080/manager/text/deploy?path=/maven-web-application&update=true"
          
        """
    }

   }  //try block end
   catch (e) {
   
       currentBuild.result = "FAILED"

  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)       //function calling
  }

	
}  //node ending


def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#2BF00E'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: '#airtel-project-team')
  
}

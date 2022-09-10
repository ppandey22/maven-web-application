node{
    
def mavenHome = tool name: "maven3.8.6"
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

stage('checkoutcode'){
git credentialsId: '6ebd65ff-4b3c-45fc-abec-c4ae5e003be6', url: 'https://github.com/ppandey22/maven-web-application.git'
}

stage('build'){
sh "${mavenHome}/bin/mvn clean package"
}
stage('sonarqubereport'){
sh "${mavenHome}/bin/mvn clean sonar:sonar"
}

stage('upload artifactory repo'){
sh "${mavenHome}/bin/mvn clean deploy"
}

stage('Deployapp into tomcatserver'){
sshagent(['af57ebd7-6199-4269-ae3f-fad256482575']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.9.11:/opt/apache-tomcat-9.0.65/webapps/"
}
}
}
def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

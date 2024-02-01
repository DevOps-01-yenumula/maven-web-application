node{

    try{

    echo "job name is: ${env.JOB_NAME}"
    echo "node name is: ${env.NODE_NAME}"
    echo "build number is: ${env.BUILD_NUMBER}"
    
    def mavenHome=tool name: "maven3.9.5"
    
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
    
    stage('CheckOutCode'){
        
    git branch: 'development', credentialsId: '4791225e-7032-4a7d-a804-fd2753751d13', url: 'https://github.com/DevOps-01-yenumula/maven-web-application.git'
    
    }
    
    stage('Build'){
        
        sh "${mavenHome}/bin/mvn clean package"
    
        }
        
    stage('ExecuteSonarQubeReport'){
        
        sh "${mavenHome}/bin/mvn sonar:sonar"
    
    }
    
    stage('UploadArtifactsIntoNexus'){
        
        sh "${mavenHome}/bin/mvn deploy"
    
    }
    
    stage('DeployAppIntoTomcat'){
        
        sshagent(['ff4bf133-7895-4528-aa33-1db8d4e916b6']) {
       
       sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.44.198:/opt/apache-tomcat-9.0.85/webapps"
    
        }
        
    }
    
}
  catch(e){
  currentBuild.result="FAILURE"
  throw e
  }//catch closing
  finally{
  sendSlackNotifications(currentBuild.result)
  }
}

def slackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'
  //buildStatus = buildStatus ? "SUCCESS":"FAILURE"

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    colorName = 'ORANGE'
    colorCode = '#FFA500'
  } else if (buildStatus == 'SUCCESS') {
    colorName = 'GREEN'
    colorCode = '#00FF00'
  } else {
    colorName = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: "#walmart")
}

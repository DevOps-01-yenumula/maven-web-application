{
    
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
    
    stage('DeployArtifactoryNexus'){
        
        sh "${mavenHome}/bin/mvn deploy"
    
    }
    
    stage('DeployAppIntoTomcat'){
        
        sshagent(['ff4bf133-7895-4528-aa33-1db8d4e916b6']) {
       
       sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.44.198:/opt/apache-tomcat-9.0.85/webapps"
    
        }
        
    }
    
}

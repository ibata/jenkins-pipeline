#!groovy
@Library('SalesforceGlobal') _ 
//read the README file first
pipeline {
   agent any
   stages {
	  stage('START') {
		steps {
			sh '''send build started notifications" '''
			// send build started notifications
			sendNotifications 'STARTED'
		}  
	   }
   }
   post {
    success {
	//send notification after build ENDED
	sendNotifications currentBuild.result
    }
    failure {
	//send notification after build ENDED
	sendNotifications currentBuild.result 
    }
    unstable {
	//send notification after build ENDED
	sendNotifications currentBuild.result
    } 
  }
}




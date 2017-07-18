
# How To send Jenkins Build Notifications to email/DL and chatter

## Pre-requisites
* setup github repo
* setup a Declarative Pipeline Project in jenkins
* Connect Pipeline to Github via credentials

### System configuration 
* Goto Jenkins Home 
* Goto Manage jenkins
* Goto Configure System  
* Goto E-mail Notification  
* --Add SMTP server: mail.internal.salesforce.com 
* --Add Default user e-mail suffix:  @salesforce.com

## Adding Notifications to pipeline
* in your jenkinsfile post section add the following
``` 
i.e 
//send email notificaiton

  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def details = """<p>${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
    <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>"""  
  emailext (  
    subject: subject,
    body: details,      
    mimeType: 'text/html',
    //email currently logged in jenkins user: 
    recipientProviders: [[$class: 'RequesterRecipientProvider']], 
    //Custom email address to send the notif TO
    to: '${env.DL_EMAIL}',
  ) 
  
//Send chatter notification with chatter rest api

  sh "curl -H \"X-PrettyPrint: 1\" -H \"Content-Type: application/json\" -d '{ \"body\": { \"messageSegments\": [{ \"type\":\"InlineImage\", \"fileId\":\"0690V0000048AGnQAM\", \"altText\":\"Build Status\" },{ \"markupType\": \"Paragraph\", \"type\": \"MarkupBegin\" }, { \"markupType\": \"Bold\", \"type\": \"MarkupBegin\" }, { \"text\": \"Job Name: ${env.JOB_NAME} \", \"type\": \"Text\" }, { \"markupType\": \"Bold\", \"type\": \"MarkupEnd\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupEnd\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupBegin\" }, { \"text\": \"Build number: #$BUILD_NUMBER\", \"type\": \"Text\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupEnd\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupBegin\" }, { \"text\": \"Build URL: $BUILD_URL\", \"type\": \"Text\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupEnd\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupBegin\" }, { \"text\": \"Jenkins URL: $JENKINS_URL\", \"type\": \"Text\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupEnd\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupBegin\" }, { \"text\": \"Node name: $NODE_NAME \", \"type\": \"Text\" }, { \"markupType\": \"Paragraph\", \"type\": \"MarkupEnd\" }] }, \"subjectId\": \"me\", \"feedElementType\": \"FeedItem\" }' -X POST \"https://na50.salesforce.com//services/data/v40.0/chatter/feed-elements\" -H 'Authorization: OAuth ${env.ACCESS_OAUTH_TOKEN}' --insecure"
......
```


# Conclusion:
This is how you get notified by email when events occur in jenkins jobs

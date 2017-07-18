
# How To send Jenkins Build Notifications using Shared Libraries

* step 1 - integrate calls to notification services like chatter and Email into our Pipeline. 
* step 2 - refactor those calls into a single Step in a Shared Library, which we’ll reuse as needed

## Pre-requisites
* setup github repo
* setup the directory structure of a Shared Library repository as follows:
```
(root)
+- src                     # Groovy source files
|   +- org
|       +- foo
|           +- Bar.groovy  # for org.foo.Bar class
+- vars
|   +- foo.groovy          # for global 'foo' variable
|   +- foo.txt             # help for 'foo' variable
+- resources               # resource files (external libraries only)
|   +- org
|       +- foo
|           +- bar.json    # static helper data for org.foo.Bar
``` 

* setup a Declarative Pipeline Project in jenkins. ie Branch Specifier= master and  Script Path= Jenkinsfile
* Connect Pipeline to Github via credentials. i.e Repository URL = git@git.soma.salesforce.com:git-org/jenkins-pipeline.git

### Setup Shared Libraries
* Goto Jenkins Home 
* Goto Manage jenkins
* Goto Configure System  
* Goto Global Pipeline Libraries 
* --Add Name 
* --Add Default version 
* --Check Load implicitly 
* --Check Modern SCM 
* --Select Git 
* --Add Project Repository 
* --Add Credentials
* Goto E-mail Notification  
* --Add SMTP server: mail.internal.salesforce.com 
* --Add Default user e-mail suffix:  @salesforce.com

## Adding Notifications
* Create a Jenkinsfile in your repo
> i.e https://git.soma.salesforce.com/git-org/jenkins-pipeline/edit/master/Jenkinsfile
* Create vars folder,  
* Create file sendNotifications.groovy 
* implement sendNotifications method into sendNotifications.groovy 
> i.e https://git.soma.salesforce.com/git-org/jenkins-pipeline-library/blob/master/vars/sendNotifications.groovy
* at the very top of the jenkinsfile add :  @Library('SalesforceGlobal') _ 
``` 
i.e 
#!groovy
 @Library('SalesforceGlobal') _ 
.....
```
* in your jenkinsfile post section add: sendNotifications currentBuild.result
``` 
i.e 
   post {
    success {
	//send notification after build ENDED
	sendNotifications currentBuild.result
    }
......
```

## We can also try to generalize this solution for DRY purposes using abstract class and methods (TBD)
``` 
i.e class relationship
abstract class BuildNotifier  implements Serializable 
|----class EmailBuildNotifier extends BuildNotifier 
|----class ChatterBuildNotifier extends BuildNotifier 


Class definition
BuildNotifier
-fields
-private methods
-abstact methods

EmailBuildNotifier
-fields
-methods

ChatterBuildNotifier
-fields
-methods


i.e
abstract class BuildNotifier  implements Serializable {
    int x, y;
    ...
    void someMethod(int newX, int newY) {
        ...
    }

    //method to implement in sub classes
    abstract void sendBuildStatus();
}


class EmailBuildNotifier extends BuildNotifier {
    void sendBuildStatus() {
        ...
    }

}

class ChatterBuildNotifier extends BuildNotifier {
    void sendBuildStatus() {
        ...
    }
}
```

# Conclusion:
This is how you get notified by email or chatter when events occur in jenkins jobs, and how to add the solution to an existing jenkins pipeline code.

source: https://jenkins.io/doc/book/pipeline/shared-libraries/

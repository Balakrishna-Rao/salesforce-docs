###Source Code and Configuration Management

[Source code Comment standards for this project](_pages/Comment-standards.md)

All methods should begin with a brief comment describing the functional characteristics of the method (what it does). This description should not describe the implementation details (how it does it) because these often change over time, resulting in unnecessary comment maintenance work, or worse yet erroneous comments. The code itself and any necessary inline or local comments will follow the standards as defined by the [SalesforceFoundation](https://github.com/SalesforceFoundation/ApexDoc#documenting-class-files):

#####Class Comments

Located in the lines above the class declaration. The special tokens are all optional.

|token | description|
|-------------|:--------|
|@Class Name | the name of the class|
|@Test class name | the test class name of the class|
|@purpose | purpose of the class|
|@Modification log | History of class modiifcation with date and purpose|


Example

```
/**
/**********************************************************************************************************
Class Name: OpportunityEditController
Test Class Name: OpportunityEditControllerTest, SynchronizeWithOpportunityTest
Purpose: Perform Credit Check before updating opportunity as Closed-Won
Modification Log:
Ver   Date             Author                         Modification 
-----------------------------------------------------------------------------
 */
public with sharing class OpportunityEditController {
```

#####Property Comments

Located in the lines above a property. The special tokens are all optional.

|token | description|
|-------------|:--------|
|@description | one or more lines that describe the property|

Example

```    
    /************************************************************************** *****************************
     * @description specifies whether state and country picklists are enabled in this org.
     * returns true if enabled.
     */
    public static Boolean isStateCountryPicklistsEnabled {
        get {
```

#####Method Comments

In order for ApexDoc to identify class methods, the method line must contain an explicit scope (global, public, private, testMethod, webService). The comment block is located in the lines above a method. The special tokens are all optional.

|token | description|
|-------------|:--------|
|@description | one or more lines that provide an overview of the method|
|@param _param name_ | a description of what the parameter does|
|@return | a description of the return value from the method|
|@example | Example code usage. This will be wrapped in  tags to preserve whitespace|

Example

```
    /*
     * @description Returns field describe data
     * @param objectName the name of the object to look up
     * @param fieldName the name of the field to look up
     * @return the describe field result for the given field
     * @example
     * Account a = new Account();
     */
    public static Schema.DescribeFieldResult getFieldDescribe(String objectName, String fieldName) {
```

#### Release Management 

####Versioning & Deploying Code and Configuration Between Environments

Versioning of code and configuration has always been a rather large pain point for project teams and developers. Salesforce itself does not support versioning of the code and configuration within an org. Project teams cannot use standard development practices to check-in both your code and configuration. 

The first problem is that developers can write apex code on their local machines or directly in the Salesforce UI. Even if they are developing code in an external tool as soon as they save their changes are being “deployed” to the Salesforce org. The developer could then “commit” the code to source control, but what if someone made a change to the code directly in the UI without the developer’s knowledge? That code would never get checked into source control.

The second problem is the management of the configuration. You cannot easily edit configuration files and then check them into source control. Instead you perform the configurations directly in the UI. It would then take a developer to download all the configuration files using a development tool such as VS Code to check them into source control. This is definitely not a feasible practice. 

If you want to create a more repeatable process to retrieve/deploy Salesforce code and configuration you would use the Salesforce metadata API along with some ANT scripts to automate the deployments. This only gets you halfway there. Even after you create the automated scripts, you still would need to manually create the ```package.xml``` file that lists all of the files that you wish to retrieve or deploy. This is a very tedious and time consuming practice.

####Copado to the Rescue!

Copado offers a feature in the UI that allows you to deploy the components of an application by adding each of the components . T
This is the basis of the deployment process outlined in this document. Below you will find a sample of what a Change Set in Salesforce looks like.



####Deploying between Salesforce Environments 

We are using Copado as the managed package solution for the CI/CD process.  

A typical basic release from one environment to another environment would proceed as follows:

1. A developer or configurator develops the solution in their dev org 
1. A developer or configurator adds code and components to that user story record in the Copado.
1. The copado will commit the added components to the Github repository by creating a feature branch  
1. Developer will create the pull request and get the approval from the peer developer and make sure the quality gates are passed in the Copado
1. The developer promotes the solution to the next environment through the copado deployment.
1. the developer promotes the solution to SIT and get approval from QA.
1. Once QA approval is done in SIT. Developer promotes to UAT and then handover the feature branch to AMS team for production deployment of the solution.


#### PackageSource Control
As mentioned above, Salesforce out of the box does not support versioning of the code and configuration. This is why we are using GitHub with individual repositories to store and version the code and configuration.


####Auditing and Tracking of a Release
There are mechanisms that are used by the release management team to ensure that appropriate approvals are given for deployments to various environments and to track all communication throughout the process.

1. **Pull Requests** - Pull requests in GitHub are a mechanism to request that code from a feature branch are merged into a corresponding parent repository master branch. Within the pull request the requester and release manager can collaborate with each other about the release.The pull request stays open till the code reviews are complete for the feature/solution. Once the peer reviewer accepts the pull request, the code is ready to be deployed to upstream environments.


####Wrapping Things Up and Walk-through the Entire Process
Now that you understand the individual components involved in the release management process, you can walk through a complete release scenario by team member role from start to finish below. 

The simulation below walks through the following:
  1. Initiation of a new feature branch.
  1. What happens on day one of development for the project.
  1. What happens every day after day one during development for the project.
  1. Promoting the solution to the QA sandbox.
  1. Promoting the solution to the UAT sandbox.
  1. Aligning with the AMS team for the production deployment
  1. What happens after deployment to production. 

###Release Management Scenario


####New Feature Initiation
A new Salesforce project has just been established. The project has team members and is ready to start development. The following are the steps that must be performed before the project team can begin development. 


**ROLE: Project Manager / Lead Developer**

1. Create a JIRA ticket for the requirement. 
1. Write the business requirment and groom the jira ticket for development.


**Lead Developer**

1. Develop the solution in the assigned sandbox 
1. Unit test the solution in the sandbox
1. Commit the solution and deploy the solution to upstream environment through Copado


**Release Manager**

1. Configure the copado pipeline and maintenace of deployment pipeline
1. Release process changes and modifications


####Day 1
Now that all of the initial project initiation steps have been completed, the project team can now begin development. 

**Developer 1**

1. Creates a new apex class in VS code and saves to the Platform Dev sandbox.
1. Creates a new LWC
1. Creates a new custom object in the Salesforce UI. 
1. Adds the new apex class, Visualforce page, and custom object to the copado feature branch.


**Copado -Retrieve and Commit job**

1. Syncs changes to the feature branch using the commit button on the user story record. 

**Note:** Only code and components that are in the user story record are committed to the repository. 

####Day 1+X Project Step
This step represents every day after the first day of development. 

 ####Deploy to Team QA
In this step the project team now has enough code and configuration completed that they are ready to start testing their application and demoing it to end users. In order to QA and demo to end using they need to deploy their application to their project team’s build / QA sandbox.

**Lead Developer**

1. Verifies the user story component and get the quality gate passed for promoting to QA(DEVINT) sandbox.

1. Performs deployment process to QA(DEVINT) sandbox.

####Deploy to UAT
In this step the project team has completed the current version of their application and is ready to start the deployment process to production. The first step in that process is deploying their application to the UAT sandbox.

**Lead Developer**

1. Verifies that the user story is tested and ready to deploy to UAT. 
1. Verify the release version in the user story
1. Update the JIRA ticket to UAT and promote the changes to UAT using Copado


**Project Team**
Tests to make sure everything is functioning as expected in the UAT sandbox.

####Deploy to Production
This is the final step in the release management process. 

**AMS Team**

1. After the UAT testing, Schedule a call with AMS team to handover the KT of the developed solution with the components list and manual task.
2. Developer is responsible to make sure the KT is done.
3. AMS will take care of the production deployments and manual task if any.
4. On the release day, AMS will do the production deployments


**Project Team**

1. Performs testing in production to validate everything is working as expected.


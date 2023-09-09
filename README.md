# Products in Apigee

In Apigee, "products" refer to a key component of the API management platform. An API product is a bundle or package that represents one or more APIs and defines the terms and conditions under which developers can access and use those APIs. API products are used to package APIs and expose them to developers, allowing them to subscribe to and consume the APIs based on the rules and policies set by the API provider. 

## Taking Backup of Product from Apigee to Github Repository using Jenkins

This repository contains Jenkinsfile which has script for taking Backup of Product from Apigee to Github Repository using Jenkins.

For using this template we have to update following values in  jenkinsfile:

`<GCP_CREDENTIAL_ID>`

`<GITHUB_CREDS_ID>`

`<GITHUB_USER_NAME>`

`<GITHUB_USER_EMAIL>`

`<APIGEE_ORG_NAME>`
            
1. Create a Jenkins pipeline using this repository. Configure your jenkins pipeline to use this github repository accordingly.

2. This jenkins pipeline contains a scheduled cronjob (weekly basis) which will run to automate the backup process at regular intervals. This ensures that backups are performed consistently without manual intervention.

3. Jenkins pipeline for backup is utilizing the Apigee Command Line Interface (Apigeecli) to export the identified Apigee resources. Apigeecli allows to interact with the Apigee API and export configurations and definitions.

4. Jenkins Pipeline for backup has a stage for cleaning the backup data of a month ago from Github Repository. Regular cleanup ensures that only relevant and recent backup files are retained.

5. When this pipeline will run and then it will create a following type of directory structure here in this repository and will store backup data in this:

`<APIGEE_ORG_NAME> > <DATE_OF_BACKUP> > products.json`

![jenkins_pipeline](https://i.postimg.cc/VLy9N8Bj/download-2.png)


6. `products.json` file will contain all the products configurations.

7. To verify this move to:

   GCP > Apigee > Products


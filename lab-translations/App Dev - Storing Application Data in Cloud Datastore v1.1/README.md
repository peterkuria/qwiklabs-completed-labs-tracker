# App Dev: Storing Application Data in Cloud Datastore v1.1

[source](https://googlepluralsight.qwiklabs.com/focuses/11068918?parent=lti_session)

## Overview

In this lab, you will review the case study application, an online Quiz. You will store application data for the Quiz application in Cloud Datastore.

The Quiz application skeleton has already been written for you. You will clone a repository that contains the skeleton using Google Cloud Shell, review the code using the Cloud Shell editor, and view it using the Cloud Shell web preview feature.

Then you will modify the code that stores data to use Cloud Datastore.


## Objectives

In this lab, you will learn how to perform the following tasks:

1. Harness Cloud Shell as your development environment.

1. Integrate Cloud Datastore into a NodeJS application.

### Tasks to do

1.  To clone the repository for the class, execute the following command:

```bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst && cd ~/training-data-analyst/courses/developingapps/nodejs/datastore/start
```

2. Set environment variables and make the repository folder your $WORKDIR where you run all commands related to this lab:
```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)' 2>/dev/null)
WORKDIR=$(pwd)
```

```
3. To export an environment variable, GCLOUD_PROJECT that references the GCP Project ID, execute the following command:

```
export GCLOUD_PROJECT=$DEVSHELL_PROJECT_ID
```

4. To run the application, execute the following command:

```
npm start
``

## Review the case study application

1. In `Cloud Shell`, click `Web preview > Preview on port 8080` to preview the quiz application.


##  Examining the Case Study Application Code

We will review the case study application code.

```
cd /training-data-analyst/courses/developingapps/nodejs/datastore/start/serve



## to delete the following line
# cat $WORKDIR/courses/developingapps/nodejs/datastore/start/serve/| grep "(request)" -A 12
```

## Adding Entities to Cloud Datastore

Return to `Cloud Shell` and stop the application by pressing `Ctrl+C`.

1. To create an App Engine application in your project, execute the following command in Cloud Shel

```
gcloud app create --region "us-central"
```




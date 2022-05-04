# Deploy a Python App with Cloud Run and GitActions.

![image](https://user-images.githubusercontent.com/32203163/166609659-73bd9d2a-7dcc-4185-bdd8-4902824c14c1.png)

*Objectives
1. Create a simple REST API with Python.
2. Write a unit test for your code.
3. Create a Dockerfile.
4. Create a GitHub Action workflow file to deploy your code on Cloud Run.
5. Make the code acessible for anyone

* Cloud Run
To make your life easier, export these environment variables so that you can copy and paste the commands used here. Choose whatever name you want, but the $PROJECT_ID has to be a unique name, because project IDs can't be reused in Google Cloud.

```bash
export PROJECT_ID=
export ACCOUNT_NAME=
```

**For example, your commands should look something like this:
```bash
export PROJECT_ID=project-example
export ACCOUNT_NAME=account-example
```

**Log in with your Google account:
```bash
gcloud auth login
```

**Create a project and select that project:
```bash
gcloud projects create $PROJECT_ID
gcloud config set project $PROJECT_ID
```
**Enable billing for your project, and create a billing profile if you don’t have one:
```bash
open "https://console.cloud.google.com/billing/linkedaccount?project=$PROJECT_ID"
```

**Enable the necessary services:

```bash
gcloud services enable cloudbuild.googleapis.com run.googleapis.com containerregistry.googleapis.com
```
**Create a service account:

```bash
gcloud iam service-accounts create $ACCOUNT_NAME \
  --description="Cloud Run deploy account" \
  --display-name="Cloud-Run-Deploy"
 ```
Give the service account Cloud Run Admin, Storage Admin, and Service Account User roles. You can’t set all of them at once, so you have to run separate commands:

```bash
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=serviceAccount:$ACCOUNT_NAME@$PROJECT_ID.iam.gserviceaccount.com \
  --role=roles/run.admin

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=serviceAccount:$ACCOUNT_NAME@$PROJECT_ID.iam.gserviceaccount.com \
  --role=roles/storage.admin

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=serviceAccount:$ACCOUNT_NAME@$PROJECT_ID.iam.gserviceaccount.com \
  --role=roles/iam.serviceAccountUser
 ```
Generate a key.json file with your credentials, so your GitHub workflow can authenticate with Google Cloud:
```bash
gcloud iam service-accounts keys create key.json \
    --iam-account $ACCOUNT_NAME@$PROJECT_ID.iam.gserviceaccount.com
```

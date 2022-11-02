# STEP 2

In this step, we will be deploying our application containers to the Kubernetes cluster we provisioned on Google Cloud Platform

First we need to create another `user` on AWS with only `programmatic access`. This is needed for our application to establish connection with our s3 bucket

Then we will upload our Application files (App.zip) into our GCP project.


ON Amazon Web Services
* Access AWS console and go to `IAM service`
* Under `Access management`, Click in `"Users"`, then `"Add user"` and create a `programmatic user` “hotel-app”
* On Set permissions, click on `"Attach existing policies directly"` button.
* Select `AmazonS3FullAccess`.
* Click on `Next: Tags`
* Click on `Next: Review`
* Click on `Create user`
* Click on `Download .csv`
* After download, rename `.csv` to `hotel-app.csv` on your system


ON Google Cloud Platform (GCP)
* Download the `App.zip` file on your system
* Navigate to `Cloud SQL` instance and create a new user app with password `welcome123456` on Cloud SQL MySQL database
* Connect to Google `Cloud Shell`
* Upload `App.zip` file to GCP Cloud Shell

Now paste the following commands on the cloud shell terminal
```
cd
mkdir step_2
mv App.zip step_2
cd step_2
unzip App.zip
```


Now you need to `connect` to `MySQL DB` running on `Cloud SQL. Run the command below (once it prompts for the `password`, provide `welcome123456`)

`mysql --host=<public_ip_cloudsql> --port=3306 -u app -p`

In the above command make sure you use the `Public IP address` of the database instance. Go to `Overview` on the Left Pane, You will see the `Public IP address`. Copy it and use it as the `<pubic_ip_cloudsql>` in the above command

* Once you're connected to the database instance, create the `products table` for testing purposes. Remember in `Step 1`, we created a database when we deployed the sql resources using `terraform`. You can check the file on terraform `(tcb_gcp_database.tf)` of `Step 1` for reference. The database that was created on top the SQL instance is called `“dbcovidtesting”`. So now run the following command

```
use dbcovidtesting;
source ~/step_2/App/en/db/create_table.sql;
show tables;
exit;
```

YOU HAVE SUCCESSFULLY CREATED A TABLE INSIDE THE DATABASE WITH THE ABOVE COMMAND. 


## NEXT IS TO BUILD THE DOCKER IMAGE OF OUR APP AND PUSH IT TO THE GOOGLE CONTAINER REGISTRY

Please replace the `<PROJECT_ID>` with your `Project ID`. ⚠ Enable Cloud Build API.

#Command to enable Cloud Build API

`gcloud services enable cloudbuild.googleapis.com`


KNOWN ISSUE DURING THIS SETUP

`ERROR: (gcloud.builds.submit) INVALID_ARGUMENT: could not resolve source: googleapi: Error 403: 989404026119@cloudbuild.gserviceaccount.com does not have storage.objects.get access to the Google Cloud Storage object., forbidden`

To solve it:

1. Access `IAM & Admin`;
2. Click on your Cloud Build `Service Account`

Example: `989404026119@cloudbuild.gserviceaccount.com` Cloud Build Service Account

3. On your Cloud Build Service Account, `right side`, click on `Edit principal`
4. Click on `Add another role`
5. Click on `Select Role`, and filter by `Storage Admin or gcs`. Select `Storage Admin (Full control of GCS resources)`.
6. Click on `Save` and go to `Cloud Shell`.


NOW GO AHEAD AND BUILD THE DOCKER IMAGE

```
cd ~/step_2/App/en/app
gcloud builds submit --tag gcr.io/<PROJECT_ID>/luxxy-covid-testing-system-app-en
```


* Open the `Cloud Editor` and edit the Kubernetes deployment file `(luxxy-covid-testing-system.yaml)` and update the variables below in red with your `<PROJECT_ID>` on the Google Container Registry path, AWS Bucket name, AWS Keys (from hotel-app.csv) and Cloud SQL Database Private IP.

```
cd ~/App/en/kubernetes
luxxy-covid-testing-system.yaml
```

```

				image: gcr.io/<PROJECT_ID>/luxxy-covid-testing-system-app-en:latest
...
				- name: AWS_BUCKET
          value: “your-unique-bucket-name-here”
        - name: S3_ACCESS_KEY
          value: "xxxxxxxxxxxxxxxxxxxxx"
        - name: S3_SECRET_ACCESS_KEY
          value: "xxxxxxxxxxxxxxxxxxxx"
        - name: DB_HOST_NAME
          value: "172.21.0.3"
  ```

* Connect to the `GKE (Google Kubernetes Engine)` cluster via `Console`
* Deploy the application Pharma Store in the Cluster

```
cd ~/step_2/App/en/kubernetes
kubectl apply -f luxxy-covid-testing-system.yaml
```

* Get the `Public IP` and test the application
* You should see the app up & running!


## CONGRATULATIONS, WE HAVE FINISHED STEP 2



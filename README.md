# hotelapp-multicloud
Implementing a Multicloud (AWS and GCP) solution for a Hotel App

# Step 1
In this step, we will be Provisioning Kubernetes and SQL on GCP. We will also create an s3 bucket on aws that will be used store the cover test pdf files.

## CREATE AN AWS ACCOUNT.

On Amazon Web Services, Create an IAM user and attach a policy to the user.
* Access AWS console and go to IAM service
* Under Access management, Click in "Users", then "Add user" and create a programmatic user "terraform-en-1"
* On Set permissions, click on "Attach existing policies directly" button.
* Select AmazonS3FullAccess.
* Click on Next: Tags
* Click on Next: Review
* Click on Create user
* Click on Download .csv
* After download, rename .csv to accessKeys.csv

## CREATE A GOOGLE CLOUD PLATFORM ACCOUNT

You are entitled to $300 worth of credit when you first register on the Google Cloud Platform. This is enough for the project we want to run. Click the link below to register https://console.cloud.google.com/

After Creating Your GCP account, you can create a new project (You can also use the default project named My Project)

## NEXT IS TO UPLOAD THE REQUIRED FILES ON YOUR GCP PROJECT.
* Check for Provisioning.zip on this repository. Download this file(This contains the terraform files to provision the necessary resources on GCP and AWS)
* Access GCP Console and open Cloud Shell
* Upload accessKeys.csv and Provisioning.zip file to GCP Cloud Shell

When you open your cloud shell, paste the following commands

```
mkdir step_1
mv Provisioning.zip step_1
cd step_1
unzip Provisioning.zip
mv ~/accessKeys.csv Provisioning/en
cd Provisioning/en
chmod +x *.sh
```

## NEXT IS TO PREPARE AWS and GCP ENVIRONMENT.

Still on the directory on the cloud shell, Run the following commands (Authorize when asked)

```
./aws_set_credentials.sh accessKeys.csv
gcloud config set project <your_project_id> 
```

In the last command, make sure you put your Project id. The command should look like this gcloud config set project my project-xx12xxx

Execute the command `./gcp_set_project.sh`

BEFORE PROVISIONING THE REQUIRED RESOURCES ON GCP IN OUR PROJECT, WE HAVE TO ENABLE THE APIS AND REGISTRY WE WANT TO USE

Enable the Container Registry API, Kubernetes Engine API and the Cloud SQL API

```
gcloud services enable containerregistry.googleapis.com 
gcloud services enable container.googleapis.com 
gcloud services enable sqladmin.googleapis.com 
```

## NEXT IS TO USE TERRAFORM TO PROVISION OUR RESOURCES ON GCP AND AWS. WE WILL BE PROVISIONING KUBERNETES AND SQL ON GCP. THEN ON AWS, WE WILL BE PROVISIONING S3 BUCKET.

* Before executing the Terraform commands, open the Google Editor and update the file tcb_aws_storage.tf replacing the bucket name with a unique name (AWS requires unique bucket names).
    * Open the tcb_aws_storage.tf using Google Editor
    * On line 4 of the file tcb_aws_storage.tf:
        * Replace bucket-name with your unique bucket name Example: my-unique-bucket

You can now run the following commands to finish provision infrastructure steps

```
cd ~/step_1/Provisioning/en/terraform/
terraform init
terraform plan
terraform apply
```
Type `Yes` and go ahead.

YOU HAVE NOW INITITIATED THE PROCESS TO PROVISION KUBERNETES AND SQL ON GCP. ALSO AN S3 BUCKET WILL BE PROVISIONED ON YOUR AWS ACCOUNT. PROVISIONING SQL AND KUBERNETES ON GCP USUALLY TAKES SOME MINUTES

* Once the CloudSQL instance is provisioned, access the Cloud SQL service
* Click on your Cloud SQL instance.
* On the left side, under Primary Instance, click on Connections.
* Under Instance IP assignment, enable Private IP.
    * Click Set up Connection and Use an automatically allocated IP range in your network.
    * Click Continue
    * Click Create Connection and wait a minutes.
* Under Associated networking, select "Default"
* Under Authorized Networks, click "Add Network".
* Under New Network, enter the following information:
    * Name: Public Access (For testing purposes only)
    * Network:** 0.0.0.0/0
    * Click Done.
PS: For production environments, it is recommended to use only the Private Network for database access. ⚠️ Never grant public network access (0.0.0.0/0) to production databases.
* After that, click on Save and wait to conclude the update.



## CONGRATULATIONS, WE HAVE FINISHED STEP 1



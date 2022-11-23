# Stage 3

In this step we will be migrating the database and cover test files from the on-premise network to the GCP and AWS respectively

Database Dump Files —> SQL Database in GCP
Covid Test Dump Files —> AWS S3 Bucket


WE WILL BEGIN WITH MIGRATING TO OUR SQL DATABASE IN GCP

Google Cloud Platform - Database Migration steps

* Download the dump `(dbDumps.zip)`
* Connect to Google `Cloud Shell`
* Upload `dbDumps.zip` file to GCP `Cloud Shell`

Run the following commands on the cloud shell
```
cd
mkdir step_3
mv dbDumps.zip step_3
cd step_3
unzip dbDumps.zip
```

YOU MUST CONNECT TO YOUR DATABASE INSTANCE IN THE CLOUD TO BEGIN MIGRATING DATABASE RECORDS

* Connect to Cloud SQL MySQL database instance with the following command

`mysql --host=<public_ip_address> --port=3306 -u app -p`

In the above command, put the `Public IP address` of your database instance


* Import the dump on Cloud SQL
```
use dbcovidtesting;
source ~/step_3/dbDumps/en/db/db_dump.sql
```


* Check if the data got imported correctly using the below sql command

`select * from records;`


NOW LETS BEGIN MIGRATING THE COVID TEST RESULT DUMPS TO OUR AWS BUCKET

Go To Your `AWS Management Console` and in the search box, search for AWS `Cloud Shell`

* Connect to the `AWS Cloud Shell`
* Upload the PDF (Copy and paste the commands below)

```
cd
mkdir step_3
mv dbDumps.zip step_3
cd step_3
unzip dbDumps.zip
```


* Sync PDF Files with your `AWS S3` used for COVID-19 Testing Status System. Replace the `bucket name` with yours. ie:

```
cd dbDumps/en/pdf_files
aws s3 sync . s3://your-unique-bucket-name-here
```

* Test the application. Upon migrating the data and files, you should be able to see the entries under `“View Guest Results”` page.


## WOWWWWW!!! Congratulations! You have migrated an "on-premises" application & database to a MultiCloud Architecture!

THAT WAS REALLY A LONG CLOUD SESSION. I HOPE YOU HAVE BENEFITTED IMMENSELY FROM THIS EXERCISE

NOT SO FAST, YOU NEED TO TAKE AWAY THE RESOURCES YOU DEPLOYED ON THE CLOUD SO YOU WOULDNT BE CHARGHED. SO DELETE ALL THE RESOURCES YOU DEPLOYED ON BOTH GCP AND AWS

How to delete the Multiple Cloud Providers resources
First step - Empty your bucket
* Access the `AWS Console` and open `S3` Service.
* Select your `bucket` and click on `Empty`.
* To confirm deletion, type `permanently delete` in the text input field.
* Click on `Empty`.

First step concluded!


Second step - Set the instance `deletion_protection` from `true` to `false`
* Open the `Google Editor`, and access the following path: `step_1/Provisioning/en/terraform/`. Now, open the `tcb_gcp_database.tf` file 
* On `line 11` of the `tcb_gcp_database.tf` file:
    * Replace from `deletion_protection = “true”` to `deletion_protection = “false”` Example:
        * First version: `deletion_protection = "true”`
        * Updated version: `deletion_protection = "false”`
* Change from Editor to Terminal 
* Run the commands to apply the terraform file changes: 
```
cd ~/step_1/Provisioning/en/terraform/
terraform apply
```
Enter a value: `yes`

Now, you’re ready to delete all the resources with the terraform destroy.

* Run the following command:
```
terraform destroy
```
Enter a value: `yes`


## Congratulations! All resources were deleted from multiple Cloud Providers with just a command!









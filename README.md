# GCP Qwiklabs Practice Project - Samples
Google Africa Developer Scholarship Phase II - list of completed sample qwiklabs for **Google Cloud Practice Project**.

Screenshot are organized based on the **Course Title**, each **link** takes to the **screenshot** of the qwicklab completed.
## List of sample completed  Labs screenshoots

* Google Cloud Platform Fundamentals - Core Infrastructure
    - [Google Cloud Fundamentals: Getting Started with Cloud Marketplace](Screenshoots/Getting-Started-with-Cloud-Marketplace.png)
    - [Google Cloud Fundamentals: Getting Started with Compute Engine](Screenshoots/Getting-Started-with-Compute-Engine.png)
    - [Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL](Screenshoots/Cloud-Storage-and-Cloud-SQL.png)
    - [Google Cloud Fundamentals: Getting Started with GKE](Screenshoots/GKE.png)
    - [Getting Started with App Engine](Screenshoots/App-Engine.png)
    - [Google Cloud Fundamentals: Getting Started with Deployment Manager and Cloud Monitoring](Screenshoots/Deployment-Manager-and-Cloud-Monitoring.png)
    - [Google Cloud Fundamentals: Getting Started with BigQuery](Screenshoots/BigQuery.png)

* Essential Google Cloud Infrastructure: Foundation
    - [Console and Cloud Shell](Screenshoots/Console-and-Cloud-Shell.png)
    - [Infrastructure Preview](Screenshoots/Infrastructure-Preview.png)
    - [VPC Networking](Screenshoots/VPC-Networking.png)
    - [Implement Private Google Access and Cloud NAT](Screenshoots/Implement-Private-Google-Access-and-Cloud-NAT.png)
    - [Creating Virtual Machines](Screenshoots/Creating-Virtual-Machines.png)
    - [Working with Virtual Machines](Screenshoots/Working-with-Virtual-Machines.png)

* Essential Google Cloud Infrastructure: Core Services
    - [Cloud IAM](Screenshoots/Cloud-IAM.png)
    - [Cloud Storage](Screenshoots/Cloud-Storage.png)
    - [Implementing Cloud SQL](Screenshoots/Implementing-Cloud-SQL.png)
    - [Examining Billing data with BigQuery](Screenshoots/Examining-Billing-data-with-BigQuery.png)
    - [Resource Monitoring](Screenshoots/Resource-Monitoring.png)
    - [Error Reporting and Debugging](Screenshoots/Error-Reporting-and-Debugging.png)

* Elastic Google Cloud Infrastructure: Scaling and Automation
    - [Virtual Private Networks (VPN)](Screenshoots/Virtual-Private-Networks-VPN.png)
    - [Configuring an HTTP Load Balancer with Autoscaling](Screenshoots/HTTP-Load-Balancer-with-Autoscaling.png)
    - [Configuring an Internal Load Balancer](Screenshoots/Configuring-an-Internal-Load-Balancer.png)
    - [Automating the Deployment of Infrastructure Using Deployment Manager](Screenshoots/Infrastructure-Using-Deployment-Manager.png)
    - [Automating the Deployment of Infrastructure Using Terraform](Screenshoots/Deployment-Terraform.png)

* Getting Started With Application Development on Google Cloud
    - [App Dev: Setting up a Development Environment v1.1](Screenshoots/App-Dev-Development-Environment.png)
    - [App Dev: Storing Application Data in Cloud Datastore v1.1](Screenshoots/Storing-Application-Data-in-Cloud-Datastore.png)
    - [App Dev: Storing Image and Video Files in Cloud Storage v1.1](Screenshoots/Storing-Image-and-Video-Files.png)


* Google Cloud Platform Big Data and Machine Learning Fundamentals
    - [Explore a BigQuery Public Dataset](Screenshoots/Explore-a-BigQuery-Public-Dataset.png)
    - [Recommend Products using ML with Cloud SQL and Dataproc](Screenshoots/Recommend-Products-using-ML-with-Cloud-SQL-and-Dataproc.png)
    - [Predict Visitor Purchases with a Classification Model with BigQuery ML](Screenshoots/Predict-Visitor-Purchases-with-a-Classification-Model-with-BigQuery-ML.png)
    - [Create a Streaming Data Pipeline for a Real-Time Dashboard with Cloud Dataflow](Screenshoots/Create-a-Streaming-Data-Pipeline-for-a-Real-Time-Dashboard-with-Cloud-Dataflow.png)
    - [Classify Images with Pre-built ML Models using Cloud Vision API and AutoML](Screenshoots/Classify-Images-with-Pre-built-ML-Models-using-Cloud-Vision-API-and-AutoML.png)



# Translation of labs from Console instructions to command line instructions

Bellow are sample of selected laboratories translated from web Console click, drag and drop instructions to 100% command line instructions.

Instructions are based on first the laboratory name then step by step commands to be excuted in the Cloud Shell.

 ## [Lab 1 - Google Cloud Fundamentals: Getting Started with Compute Engine](Compute-Engine.md)



First, set default zone to the one set by qwicklab assigned zone which is ```us-central1-a``` in my case.

```gcloud config set compute/zone us-central1-a```

Create a virtual machine named ``` my-vm-1 ``` on GCP Console (translated to command)

```
gcloud beta compute instances create my-vm-1 \
--zone=us-central1-a --machine-type=n1-standard-1 \
--subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE \
--tags=http-server --image=debian-10-buster-v20200902 --image-project=debian-cloud \
--boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=instance-1 \
--no-shielded-secure-boot --no-shielded-vtpm --no-shielded-integrity-monitoring --reservation-affinity=any
```

Create another virtual machine to test connection connection between two virtual machines in the same network.

```
gcloud compute instances create "my-vm-2" \
--machine-type "n1-standard-1" \
--image-project "debian-cloud" \
--image "debian-9-stretch-v20190213" \
--subnet "default"
```

Connect to ```my-vm-2``` using SSH from  the cloud shell.

``` gcloud beta compute ssh --zone "us-central1-a" "my-vm-1"```

check if connection to ```my-vm-1``` works or not using ```ping``` command.

``` ping -c 4 my-vm-2 ```


Install the Nginx web server on ```my-vm-1```. 

```
sudo apt-get update -y && \
sudo apt-get install nginx-light -y

```

Use the Linux ```sed``` command to display a custom message to the home page of the web server, from *Welcome to nginx!* to *Hi* from my full name.

```sed -i 's/Welcome to nginx!/Hi from Tsegaye Mekonnen/g' /var/www/html/index.nginx-debian.html```


Confirm if the web server is serving your new page updated page. At the command prompt on my-vm-1, execute this command:


``` curl http://my-vm-1/```

Login to the second virtual machine and confirm if the page from my-vm-1 is accessible.

``` gcloud compute ssh my-vm-2 --zone us-central1-a --internal-ip ```

``` curl http://my-vm-1/```

End of laboratory for Google Cloud Fundamentals: Getting Started with Compute Engine.

 ## [Lab 2 - Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL](Cloud-Storage-SQL.md)

### Overview
In this lab, a Cloud Storage bucket is created to place an image in it. Compute Engine is configured to use a database managed by Cloud SQL. An application running in this lab, is configured as a web server with PHP, a web development environment that is the basis for popular blogging software.

First deploy a web server VM instance
```
gcloud beta compute instances create bloghost \
--zone=us-central1-a --machine-type=n1-standard-1 --subnet=default \
--metadata=startup-script=apt-get\ update$'\n'apt-get\ install\ apache2\ php\ php-mysql\ -y$'\n'service\ apache2\ restart \
--tags=http-server --image=debian-9-stretch-v20200902 \
--image-project=debian-cloud --boot-disk-size=10GB \
--boot-disk-type=pd-standard --boot-disk-device-name=bloghost
```

For convenience, enter your chosen location into an environment variable called LOCATION

``` 
export LOCATION=US
```



In Cloud Shell, the DEVSHELL_PROJECT_ID environment variable contains the current project ID. Enter this command to make a bucket named as the project ID:

```
gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID
```

Retrieve a banner image from a publicly accessible Cloud Storage location to be used in a sample PHP web application for later.

``` 
gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png
```

Copy the file from cloud shell to the storage bucket permanenetly named after the project ID.

``` 
gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
```


Modify the Access Control List of the sample image  object so that it is readable by everyone, will have accsessible publick url as ``` https://storage.googleapis.com/$DEVSHELL_PROJECT_ID/my-excellent-blog.png```:


``` 
gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
```


Create the **Cloud SQL** instance console translation to command:

```
gcloud sql instances create blog-db \
--tier=db-n1-standard-2 --zone=us-central1-a \
--database-version=MYSQL_5_7
```

Set a Root user password sample as  ```pass1234```.

```
gcloud sql users set-password root --host=% --instance blog-db \
--password pass1234

```

when the command finshes running we can try if our cloud SQL instance is working using the user root and the password just given in the upper command.


```
gcloud sql connect blog-db2 --user=root --quiet
```

type the password and play with the mysql dattabase by trying some commands

```
show databases;
create database sampleDB;
use sample DB;
exit;

```

ADD another **USER ACCOUNT** to the cloud SQL instance 

```
gcloud sql users create blogdbuser \
--host=% --instance=blog-db \
--password=pass1234
```

Add the Public IP address of the above VM instance created in the first step so that our VM instance could access the cloud SQL istance and store some data.

```
gcloud sql instances patch blog-db \
--authorized-networks=35.224.39.178/32
```

Configure a PHP application in a Compute Engine instance to use Cloud SQL:

Firstly, ssh into the *bloghost* VM instance

```
gcloud beta compute ssh --zone "us-central1-a" "bloghost" 
```

Create index.php file inside document root of the web serve since apache2 webserver is installed in the startup script of the program in the first step.


```
sudo touch /var/www/html/index.php
```
Make index.php file accesible by granting full permission so that we could modify it's contenet.

```
sudo chmod 777 /var/www/html/index.php
```

write  muptiple lines of PHP code to test if the Cloud SQL instance connects to our bloghost VM instance. IP address should match to the Public IP of the bloghost VM instance.

```
sudo cat << EOF >> /var/www/html/index.php
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>

<?php
\$dbserver = "35.192.162.183";
\$dbuser = "blogdbuser";
\$dbpassword = "pass1234";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

\$conn = new mysqli(\$dbserver, \$dbuser, \$dbpassword);

if (mysqli_connect_error()) {
        echo ("Database connection failed: " . mysqli_connect_error());
} else {
        echo ("Database connection succeeded.");
}
?>
</body></html>
EOF

```

Restart the web server, so that it could reflect the changes made to the file.

```
sudo service apache2 restart
```
check if the database connection works by using the  command line terminal based browser program.

```
curel http://bloghost/index.php
```

Our Database connection succeeded. It's time now to try if our application could access data from the cloud storage.


Add the following line to add reference to file in the fifith line after the website header ```<h1>Welcome to my excellent blog</h1>```.


```
sed -i '5i\<img src="https://storage.googleapis.com/qwiklabs-gcp-03-2d1188a512a4/my-excellent-blog.png" />' /var/www/html/index.php

```

use a terminal based web browser to check if we have both the  ```Database connection succeeded ``` and our image url.

```
curel http://bloghost/index.php
```
or use your web browser to check the sample PHP application intagrated with Database and Object storage as.

```
http://35.192.162.183/index.php
```

Laboratory complted.
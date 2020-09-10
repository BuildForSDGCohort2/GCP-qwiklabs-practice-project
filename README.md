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
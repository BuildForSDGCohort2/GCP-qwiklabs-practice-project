## Google Cloud Fundamentals: Getting Started with Compute Engine


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
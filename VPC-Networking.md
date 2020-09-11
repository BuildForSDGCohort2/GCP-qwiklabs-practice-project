#VPC Networking

Google Cloud Virtual Private Cloud (VPC) provides networking functionality to Compute Engine virtual machine (VM) instances, Kubernetes Engine containers, and the App Engine flexible environment. 


### Delete the Firewall rules
***

There  are 4 Ingress firewall rules for the default network, we can explore this laboratory by deleting each of them one by one or together by passing each name to one delete command.

    - default-allow-icmp
    - default-allow-rdp
    - default-allow-ssh
    - default-allow-internal


```
gcloud compute firewall-rules delete default-allow-icmp
```


```
gcloud compute firewall-rules delete \
    default-allow-internal  \
    default-allow-rdp \
    default-allow-ssh
```

Delete the default network

There is a single automode ```default``` network for each project, in this laboratory we delete it, and we create our own network.


```
gcloud compute networks delete default
```


Try to create a VM instance

Since there is no more network any atempt to create any VM instance will fail.


```
gcloud compute instances create instance-1 \
--zone=us-central1-a --machine-type=n1-standard-1 \
--image=debian-9-stretch-v20200902 \
--image-project=debian-cloud --boot-disk-size=10GB \
--boot-disk-type=pd-standard --boot-disk-device-name=bloghost
```

The above command should fail becuse there is no network to assosiate ```instance-1 ```with, we cannot create a VM instance without a VPC network.

### Create an auto mode VPC network with firewall rules
***

```
gcloud compute networks create mynetwork \
--subnet-mode=auto --bgp-routing-mode=regional
```

The above created network ```mynetwork``` have standard firewall rules that deny-all-ingress and allow-all-egress rules because they are implied. We can further create firewall rules to allow **ICMP, internal, RDP, and SSH** rules to the network ```mynetwork```.

```
gcloud compute firewall-rules create mynetwork-allow-icmp \
--network=mynetwork --direction=INGRESS --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp

gcloud compute firewall-rules create mynetwork-allow-rdp \
--network=mynetwork --direction=INGRESS --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

gcloud compute firewall-rules create mynetwork-allow-ssh \
--network=mynetwork --direction=INGRESS --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22
```

### Create a VM instance in us-central1

Since we have an automode  VPC network and firewalls, we can create VM instances from here.


```
gcloud beta compute instances create mynet-us-vm \
--zone=us-central1-c --machine-type=n1-standard-1 \
--network=mynetwork
```

### Create a VM instance in europe-west1
we could create another VM in another Region in ```europe-west1```.

```
gcloud beta compute  instances create mynet-eu-vm \
--zone=europe-west1-c --machine-type=n1-standard-1 \
--subnet=mynetwork \
```

### Convert the network to a custom mode network

To prevent new subnets automatically created as new regions become available, we can convert our automode network it to a custom mode network.

```
gcloud compute networks update mynetwork \
          --switch-to-custom-subnet-mode
```

### Create custom mode networks
***

Create a custom mode network called ```managementnet``` with a subnet ```managementsubnet-us`` for ```us-central1`` region in the IP range of 10.130.0.0/20.


```
gcloud compute networks create managementnet  --subnet-mode=custom --bgp-routing-mode=regional

gcloud compute networks subnets create managementsubnet-us --range=10.130.0.0/20 --network=managementnet --region=us-central1
````

Create another custom mode network named ```privatenet``` with two subnets ```privatesubnet-us``` and ```privatesubnet-eu```

```
gcloud compute networks create privatenet --subnet-mode=custom

gcloud compute networks subnets create privatesubnet-us \
--network=privatenet --region=us-central1 --range=172.16.0.0/24

gcloud compute networks subnets create privatesubnet-eu \
--network=privatenet --region=europe-west1 --range=172.20.0.0/20

```
To list the available VPC networks(3 CUSTOM mode networks), run the following command

```
gcloud compute networks list
```

To list the available VPC subnets (sorted by VPC network):

```
gcloud compute networks subnets list --sort-by=NETWORK
```

### Create the firewall rules for managementnet
***

A firewall rule to allow SSH, ICMP, and RDP ingress traffic to VM instances on the managementnet network can be created as follows.

```
gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp \
--direction=INGRESS --network=managementnet \
--action=ALLOW --rules=tcp:22,tcp:3389,icmp \
--source-ranges=0.0.0.0/0
```

Create the firewall rules for privatenet

```
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp \
--direction=INGRESS --network=privatenet \
--action=ALLOW --rules=icmp,tcp:22,tcp:3389 \
--source-ranges=0.0.0.0/0
```

To list all the firewall rules (sorted by VPC network) created in the current Project can be viewed as:

```
gcloud compute firewall-rules list --sort-by=NETWORK
```

### Create the ```managementnet-us-vm``` instance
We can create new Virtual Machines in the network of our preference.


```
gcloud beta compute instances create managementnet-us-vm \
--zone=us-central1-c --machine-type=f1-micro \
--subnet=managementsubnet-us
```
### Create the ```privatenet-us-vm``` instance

```
gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us
```

### To list all the VM instances (sorted by zone)
***
```
gcloud compute instances list --sort-by=ZONE

```

### Explore the connectivity across networks
***
By SSH to the ```mynet-us-vm``` instance, we can check connecting to all the vM instance in diffrent subnets and regions using Public and private IP adress.

```
gcloud beta compute ssh --zone "us-central1-c" "mynet-us-vm"

```

Using Public IP addres of the VM instances 
- mynet-eu-vm
- managementnet-us-vm
- privatenet-us-vm

works all fine cause ping the external IP address of all VM instances, even though they are in either a different zone or VPC network.
```
ping -c 3 35.187.95.35 
ping -c 3 34.123.69.192
ping -c 3 34.122.154.13
```
### Ping the internal IP addresses

You can ping the internal IP address of ```mynet-eu-vm``` because it is on the same VPC network as the source of the ping (```mynet-us-vm``), even though both VM instances are in separate zones, regions, and continents!

```
ping -c 3 10.132.0.2
```


But tring to ping internal IP address of ```managementnet-us-vm``` and ```privatenet-us-vm``` returns a 100% packet loss, because they are in separate VPC networks from the source of the ping (```mynet-us-vm```), even though they are all in the same zone, us-central1-c.
```
ping -c 3 172.16.0.2
ping -c 3 10.130.0.3

```

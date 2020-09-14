# Configuring VPC Network Peering

## Overview

In this lab, you configure VPC network peering between two networks. Then you verify private communication between two VMs in those networks, as illustrated in this diagram:

network_diagram.png

VPC network peering allows you to build SaaS (Software as a service) ecosystems in Google Cloud, which makes services available privately across different VPC networks within and across organizations. This allows workloads to communicate in private RFC 1918 space.

VPC network peering gives you several advantages over using external IP addresses or VPNs to connect networks, including:

Network Latency: Public IP networking results in higher latency than private networking.

Network Security: Service owners do not need to have their services exposed to the public internet and deal with its associated risks.

Network Cost: Google Cloud charges egress bandwidth pricing for networks using external IPs to communicate, even if the traffic is within the same zone. If, however, the networks are peered, they can use internal IPs to communicate and save on those egress costs. Regular network pricing still applies to all traffic.

Objectives
In this lab, you learn how to perform the following tasks:

Explore connectivity between non-peered VPC networks
Configure VPC network peering
Verify private communication between peered VPC networks
Delete VPC network peering
For each lab, you get a new GCP project and set of resources for a fixed time at no cost.



## Task 1. Explore connectivity between non-peered VPC networks

Each Google Cloud project starts with the default network. In addition, mynetwork, privatenet, and managementnet have been created for you along with firewall rules to allow ICMP-SSH-RDP traffic and four VM instances

1. Examine the VPC networks  and subnets from a gcloud command-line

```
export REGION=[replace with your Network region]

gcloud compute networks list

# Examine the IP addresses ranges for the subnets of my
gcloud compute networks subnets list
```

2. Examine the IP addresses ranges for the subnets of mynetwork

```
gcloud compute networks subnets list \
   --network=mynetwork
```

3. Examine the IP addresses ranges for the subnets of privatenet

```
gcloud compute networks subnets list \
   --network=privatenet
```

The subnets of privatenet fit within the 172.16.0.0/24 and 172.20.0.0/24 CIDR blocks. They do not overlap with the 10.128.0.0/9 CIDR block of mynetwork.

You can configure VPC network peering between mynetwork and privatenet because their subnets' CIDR blocks are non-overlapping.

4. Describe the subnet using the following gcloud command, replacing SUBNET_NAME with its name and REGION with its region.

```
gcloud compute networks subnets describe  SUBNET_NAME \
--region=REGION

```

## Explore the connectivity between mynetwork and privatenet

1. List existing peering connections to view their status and whether they're importing or exporting custom routes.


```
gcloud compute networks peerings list \
    --network=[MY-LOCAL-NETWORK]
```

2. Notice that none of the routes have a peering connection as the `Next hop`. Replace YOUR_PROJECT_ID with your project's ID

```
gcloud compute routes list --project <YOUR_PROJECT_ID>
```

3. Run the following command to list all the firewall rules(sorted by VPC network)


```
gcloud compute firewall-rules list --sort-by=NETWORK
```

Notice the allow `SSH` and `ICMP` firewall rules for `mynetwork` and `privatenet`.

These firewall rules have been created for you.

4. Run the following command to list all the VM instances

```
gcloud compute instances list
```

Notice the mynet-eu-vm, mynet-us-vm, privatenet-us-vm, and managementnet-us-vm instances.
These VM instances have been created for you.

5. Note the internal and external IP addresses for privatenet-us-vm.

```
sudo ifconfig

```

6. For mynet-us-vm, click SSH to launch a terminal and connect.
7. To test connectivity to privatenet-us-vm's external IP, run the following command, replacing privatenet-us-vm's external IP:

ping -c 3 <Enter privatenet-us-vm's external IP here>

This should work!

To test connectivity to privatenet-us-vm's internal IP, run the following command, replacing privatenet-us-vm's internal IP:

ping -c 3 <Enter privatenet-us-vm's internal IP here>

This should not work, as indicated by a 100% packet loss! But why?
On the VM instances page, click Columns, and then select Network.
The mynet-us-vm and privatenet-us-vm instances are in the same zone (us-central1-a) but in different VPC networks (mynetwork and privatenet). Because VPC network peering has not been configured between those networks, private communication fails between the instances of those networks.
Close the SSH terminal to mynet-us-vm:



-------------------------
You will return to this page after configuring the VPC network peering connection.

Run the following command to list all the VM instances (sorted by zone):
gcloud compute instances list --sort-by=ZONE

You should be able to ping the internal and external IP addresses of privatenet-us-vm from mynet-us-vm. 

Click `Product & services > Compute > Compute Engine > VM instances`.

SSH into mynet-us-vm instance.

In the SSH shell of privatenet-us-vm, run the following command replacing <INTERNAL_IP_OF_VM_A> with the privatenet-us-vm instance INTERNAL_IP:

```
ping -c 3 <INTERNAL_IP_OF_PRIVATE_US_VM>
```

Repeat the same test by running the following

```
ping -c 3 <EXTERNAL_IP_OF_PRIVATE-US-VM>
```

The routes to peered network CIDR prefixes are now visible across the VPC network peers. These routes are implicit routes generated for active peerings. They don't have corresponding route resources. The following command lists routes for all VPC networks for project-a.

```
gcloud compute routes list --project <FIRST_PROJECT_ID>
```


Notice that none of the routes have a peering connection as the Next hop.

You will return to this page after configuring the VPC network peering connection.



Task 2. Configure VPC network peering
VPC network peering can be configured for different VPC networks within and across organizations. Configure the following peering connections in this project:

peering-1-2: Peer mynetwork with privatenet
peering-2-1: Peer privatenet with mynetwork
Each side of a peering association is set up independently. Peering is active only when the configuration from both sides matches.

1. Create peering 1-2

Peer mynetwork with privatenet.

Run the following on the gcloud shell, Use the --import-custom-routes and --export-custom-routes flags to import or export custom routes from or to the peered network.

Replace [PEERING-NAME] with peering-1-2, network [MY-LOCAL-NETWORK] with mynetwork; [PEER-NETWORK-NAME] with privatenet

You won't need the project ID (--peer-project [PEER-PROJECT-ID]) because you are connecting to a VPC network within the same project.

```
gcloud compute networks peerings create [PEERING-NAME] \
    --auto-create-routes \
    --network=[MY-LOCAL-NETWORK] \
    --peer-network [PEER-NETWORK-NAME] \
    [--import-custom-routes] \
    [--export-custom-routes]
```

2. Create peering 2-1
Peer privatenet with mynetwork.

```
gcloud compute networks peerings create peering-2-1 \
    --auto-create-routes \
    --network=privatenet \
    --peer-network mynetwork \
    [--import-custom-routes] \
    [--export-custom-routes]
```


## Task 3. Verify private communication between peered VPC networks

gcloud compute networks peerings list-routes - [list received or advertised routes for a VPC network peering](https://cloud.google.com/sdk/gcloud/reference/compute/networks/peerings/list-routes)

Verify private RFC 1918 connectivity across mynetwork and privatenet.

Verify routes between networks
Verify that routes have been established between mynetwork and privatenet.

```
gcloud compute routes list 

# gcloud compute routes list --project <PROJECT_ID>
```

The routes to peered network CIDR prefixes are now visible across the VPC network peers. These routes are implicit routes generated for active peerings. They don't have corresponding route resources. The following command lists routes for all VPC networks for project.




## Task 4. Delete the VPC Peering Connection

Delete the VPC Peering connection and verify the deletion.

Delete the peering connection
Delete the peering-1-2 connection.



```
# gcloud compute networks peerings delete NAME --network=NETWORK [GCLOUD_WIDE_FLAG …]

gcloud compute networks peerings delete peering-1-2 --network=mynetwork
```

## Extras


Create a custom network in projects
In this lab you you have been provisioned 2 projects, the first project as a Project A and second as Project B.

For managing two projects start a new cloud shell by click + icon.

In the second cloud shell, set project ID by running the following, replacing <PROJECT_ID2> with Project ID for the 2nd project from the Qwiklabs page where you started the lab:

gcloud config set project <PROJECT_ID2>

Project-A:
Go back to first cloud shell and create a custom network:

gcloud compute networks create network-a --subnet-mode custom

Create a subnet within this VPC and specify a region and IP range by running:

gcloud compute networks subnets create network-a-central --network network-a \
    --range 10.0.0.0/16 --region us-central1

Create a VM instance:

gcloud compute instances create vm-a --zone us-central1-a --network network-a --subnet network-a-central

Run the following to enable SSH and icmp, because you'll need a secure shell to communicate with VMs during connectivity testing:

gcloud compute firewall-rules create network-a-fw --network network-a --allow tcp:22,icmp



The routes to peered network CIDR prefixes are now visible across the VPC network peers. These routes are implicit routes generated for active peerings. They don't have corresponding route resources. The following command lists routes for all VPC networks for project-a.

gcloud compute routes list --project <FIRST_PROJECT_ID>


##########################################

```
gcloud compute networks create NAME --project=$PROJECT_ID --subnet-mode=custom --bgp-routing-mode=regional

gcloud compute networks subnets create NAME --project=$PROJECT_ID --range=IP_RANGE --network=NAME --region=REGION


## add a subnet
gcloud compute networks subnets create SUBNET_NAME \
    --network=NETWORK \
    --range=PRIMARY_RANGE \
    --region=REGION

## delete a subnet

gcloud compute networks subnets delete SUBNET_NAME \
    --region=REGION


## Expand the primary IP range of a subnet with the following gcloud command:

gcloud compute networks subnets expand-ip-range SUBNET_NAME \
  --region=REGION \
  --prefix-length=PREFIX_LENGTH
```


gcloud compute routes list
NAME
gcloud compute routes list - list non-dynamic Google Compute Engine routes
SYNOPSIS
gcloud compute routes list [NAME …] [--regexp=REGEXP, -r REGEXP] [--filter=EXPRESSION] [--limit=LIMIT] [--page-size=PAGE_SIZE] [--sort-by=[FIELD,…]] [--uri] [GCLOUD_WIDE_FLAG …]
DESCRIPTION
gcloud compute routes list displays all custom static, subnet, and peering routes in VPC networks in a project.
To list custom dynamic routes learned by Cloud Routers, query the status of the Cloud Router that learned the route using


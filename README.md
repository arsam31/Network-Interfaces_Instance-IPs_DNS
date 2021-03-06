An EC2 instance always start with one primary network interface called elastic network interface.

Every EC2 has at least one primary network interface

Optionally,we can attach one or more secondary elastic network interface(ENI),which can be in a separate subnets but everything needs to be in the same AZ

Remember EC2 is isolated in one AZ, so EC2 instance can have interfaces in separate subnetes but all in one AZ

These network interfaces have different attributes

When we are attracting with an instance with a network perspective we often see elements(attributes) of primary network interface

It means when we launch an instance with security groups,those security groups are actually on the network interfaces, not the instance.

Important: Network interfaces have a MAC address,this is the hardware address of the interface and it's visible inside the OS

Each interface has a private IPv4 address

So when you select a vpc and a subnet for an instance, what youa are actually doing picking the vpc and subnet for primary network ineterface.

Now you can have zero or more secondary private IP addresses on that interface

You can have zero or one public IP addresses on that interface

You can have one elstic IP address per private IP version 4 address

Elastic IP addresses are public IPv4 addresses and these are different than normal public IPv4 addresses

With elastic IP addresses, you can have one public elastic IP address per private IP address on that interface.

You can have zero or more IPv6 addresses per instance

A security that applied on a particular interface will impact all IP addresses on that interface

Important: If you are in a situation where you need different IP addresses for an instance impacted by diffrenet security groups then you need to create multiple interfaces with those IP addresses separately and then apply different security groups to each of those interfaces

Security groups are attached to interfaces and then per instance, you can enable or disable the source and sestination check

This means if the traffic is on that interface, it's going to be discarded if it's not from one of the IP addresses on the interface as a source, or destined to oneof the IP addresses on the interface as a destination.

So if this is enabled, traffic is discarded. (This is the setting that you need to disable for an instance to work as a NAT instance, so this check needs to be switched off)

The capabilties of secondary interfaces are the same as primary, expact that you can detach secondary interfaces and move them to other EC2 instances.

Important: when you stop an instance public IPv4 is deallocated with it and when you start that instance again a brand new public IPv4 is allocated to it BUT if you RESTART that instance the public IPv4 will NOT change.

Important: when you stop an instance private IPv4 remains attach with it even when you restart it because primary private IPv4 is attached with it till the life time of that instance.

In simple words, anything that makes that instance change between EC2 hosts will also cause an IP change.

For the public IPv4 address, EC2 instance is also allocated a public DNS name

Remember How VPC works, the public IPv4 address is not directly attached to the instance or any interface, it's associated with it and the internet gateway handles the translation

So in order to allow instances in a vpc to use the same DNS name,this DNS will resolve the private IPv4 address of the instance,so the primary network interface.

Outside the vpc, the DNS will resolve to the public IPv4 address of that instance

Important: Elastic IP addresses are something that is allocated to your aws account, when you allocate an elastic IP, you can associate the elastic IP with a private IP either primary interface or with the secondary interface, If you associate elastic IP with the primary interface , then as soos as you do that , the normal non-elastic public IP version 4 address the instance had, that's REMOVED.And the elastic IP becomes the instance's new public IPv4 address.

Important: And if you remove that elastic IP address, the insterface will gain a new public IPv4 address

Question: If an instance has non elastic IP address and you assign as elastic IP and then remove it. IS there any way to get the original non-elastic IP address back?? The answer is "NO".


Important points from a networking perspective are :

Instance can have one or more network interface, aprimary and optionally can have secondaries

And then for each network interface, can have a primary private IP addresses,secondary private zaddresses,optionally one public IPv4 address, and then optionally one or more elastic IP addresses.


Exam tips: 

Tip about secondary elastic network interface and MAC address:- A lot of softwares are lisenced using a MAC address. A MAC address is static and does not change. But because EC2 is a virtualized environment, so we can swap and change elastic network interfaces.

so if you provision a secondary network interface on an instance,and you use that secondorys network interface's MAC address for lisencing. Then that means you can detach that secondary interface and attach it to a new instance and move that lisencing between different EC2 instances.

Next tip is :- Multiple interfaces can be used for multi-homed systems,so an instance with an ENI in two different subnets, you might use one for management and one for data, it gives you some flexibility.

and why you might use multiple interfaces,rather than just multiple IPs,is that security groups are attached to interfaces.

So it means if you need different rules, so different security groups, for different IPs, or different rules for different types of access based on IPs your instance has, then you need multiple elastic network interfaces with different security groups on each.

Tip for EC2 IP addressing :- OS never sees the IPv4 public address.This is provided by by a process called NAT,which is performed by internet gateway and so far as the operating system concerned,you always configure the private IPv4 address on the interface,ALWAYS.

inside the OS, it has no visibility on the network configuration of public IP address. you will never be in a situation where you need to configure windows or linux with IPv4 public address.

Important: You NEVER configure a network interface inside an operating system with a public IPv4 address inside AWS

The public DNS which is given to the instance for the public IPv4 address,this will resolve to primary IPv4 address from within the VPC, and this is done so that if you have got instance-to-instance communication,using the address inside the VPC,it never leaves VPC



## Amazon Machine Images (AMI)

When we launch an ec2 instance, we are actually using aws AMI to launch an instance through aws console UI

There are also customer provided AMIs that includes commercial softwares. In that type of case, we pay for the normal instance price plus extra amount for the commercial software

AMIs are regional, we can have different AMIs for same things in different region with unique IDs.

AMIs also include permissions:

1: Only your account can use them 

2: Only specific accounts can use them 

3: Everyone can use them

We can create instances from AMI

We can do the reverse, create AMI from an instance to capture the current configuration of that instance and then create many instances from that AMI

LifeCycle of AMI has 4 phases:

1: Launch :- this step is the one that we normally do when launching an instance from AWS console. EBS volumes are attached to instances using block device IDs

Boot volume are usually "dev/xvda"

Data volumes are usually "dev/xvdf"

These both are device IDs and this is how EBS volume is presented to an instance

2: Configure : - When the instance is in launch phase you can do some customization according to your need and launch it. 

3: Create Image : - Then make AMI of that particular instance 

4: Launch : - And launch many more instances according to your need

Think of AMI as a container. It is logical container which has associated information. It's got an ID and permissions who can use it.

The important part is when you create an AMI of an istance,the EBS volume that are attached to it, we take snapshots of that EBS volumes. Remember the first snapshot is the complete copy of your data, and onwards the snapshots are INCREMENTAL(when we change some thing in the volume,only that part is taken and added in the snapshot)

When we make an AMI the first thing that happen is the snapshots of the volume. These snapshots are referenced as Block Device Mapping in the AMI.

Block device mapping is just a table of data. It links snapshot Ids you have just created from volume(xvda,xvdf) and map those IDs to the AMI

Now what this mean is, when we use this AMI to create an instance, the instance will have the same EBS volume configuration as the original.

When you launch the instance using AMI, what actually happen is the EBS volume is created in the same AZ you are launching the instance into, and those volumes are attached to that new instance, using the same device IDs that it contained in that Block Device Mapping.



IMPORTANT Points:-   AMIs are regional service, it means we can make instances in all AZs in the same region using that AMI but can not make instance in another region

BUT what we can do is COPY the AMI in another region first. and then launch instance from that newly copied AMI.

AMI BAKING is a term which means taking an instance, isntalling all the desired softwares,changing configurations and then baking all that into AMI.

AMI can not be edited. If you want to edit it and change the configuration, then you must launch an instance and during launch phase change the configuraion and then create a brand new AMI of the instance. You can not edit existing AMI

Default permission on AMI is,it's only accessable with in your account, but we can change parameters (public, your account,specific accounts)

AMI does have a cost for the snapshots of EBS volume. Remember we are charged for how much data is stored in the volume NOT the total allocated size of volume







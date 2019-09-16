# Essential Cloud Infrastructure: Foundation

In this module we introduce the **Architecting with Google Compute Engine specialization**.

This specialization is defined for cloud solution architects, DevOps engineers, and anyone who's interested in using GCP, to create new solutions or to integrate existing systems, application environments, and infrastructure with a focus on Compute Engine.

## Content



## Module 1: Introduction to GCP

- [video #1](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/JrXPD/google-cloud-platform-gcp-infrastructure)
- [video #2](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/5DIZY/using-gcp)

In this module, we will provide you with an introduction to GCP by building on what you learned about the GCP infrastructure from the course introduction.

### GCP infrastruture

<img src="../images/GCP_services_continuum.png"
        alt="GCP_services_continuum.png"
        style="float: left; margin-right: 10px;" />

Analogy of infrastructure and applications:

<img src="../images/Analogy_infrastruture.png"
        alt="Analogy_infrastruture.png"
        style="float: left; margin-right: 10px;" />

4 parts covering the Cloud infrastructure:

1. **Foundation of essential infrastructure**: the basic technologies.
2. **The Core Services**: the building blocks of the essential infrastructure.'
3. **The Augmented infrastruture**: the systems built on top of the essential infrastructure, for scaling and automation.
4. **The application infrastructure**: consisted of containers and services specifically provided to make application development easy.

<img src="../images/GCP_cloud_infrastructure_domains.png"
        alt="GCP_cloud_infrastructure_domains.png"
        style="float: left; margin-right: 10px;" />

### Using GCP

# ways to interact with GCP

1. The **GCloud console** (http:://console.cloud.google.com)
2. The **Google Cloud SDK** to use `gcloud` in a terminal window
3. **CloudShell**: a browser-based terminal environment for GCP, accessible from the GCP console.


<img src="../images/3_ways_to_interact_with_GCP.png"
        alt="3_ways_to_interact_with_GCP.png"
        style="float: left; margin-right: 10px;" />

Working with other client libraries:

<img src="../images/GCP_client_libraries.png"
        alt="GCP_client_libraries.png"
        style="float: left; margin-right: 10px;" />


### Lab 1: Console and Cloud Shell

- video lab
- [notes lab](../labs/lab_console_cloudshell.md)


### Projects

[video](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/aqJJ9/demo-projects)

### Lab 2: Infrastructure Preview (Jenkins with Cloud Deployment Manager in no time)

[video](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/xTlIl/lab-2-infrastructure-preview-overview-and-objectives)








## Module 2: Virtual Networks

In this module, we start by introducing **Virtual Private Cloud (VPC)** which is Google’s **_managed networking functionality_** for your Cloud Platform resources. Then we dissect networking into its fundamental components, which are:

- projects,
- networks,
- subnetworks,
- IP addresses,
- routes and firewall rules,
- along with network pricing.

- [video #1: Google Cloud Platform (GCP) VPC](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/vJbfe/google-cloud-platform-gcp-vpc)
- [video #2: Projects, networks, and subnetworks](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/FyKc4/projects-networks-and-subnetworks)


### Projects, networks, and subnetworks

[video](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/FyKc4/projects-networks-and-subnetworks)

<img src="../images/Example_5_networks_and_interconnections.png"
        alt="Example_5_networks_and_interconnections.png"
        style="float: left; margin-right: 10px;" />

<img src="../images/Example_5_networks_and_interconnections_regions_and_zones.png"
        alt="Example_5_networks_and_interconnections_regions_and_zones.png"
        style="float: left; margin-right: 10px;" />

<img src="../images/Example_5_subnetworks_managing_resources.png"
        alt="Example_5_subnetworks_managing_resources.png"
        style="float: left; margin-right: 10px;" />

### IP addresses

[video](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/VBh8c/ip-addresses)

<img src="../images/IP_addresses.png"
        alt="IP_addresses.png"
        style="float: left; margin-right: 10px;" />

<img src="../images/External_IP_addresses_always_mapped_to_internal_IP_Addresses.png"
        alt="External_IP_addresses_always_mapped_to_internal_IP_Addresses.png"
        style="float: left; margin-right: 10px;" />

In case of restart, the internal IP address may change, but the DNS system points to instances which keep the external IP address unchanged.

### Routes and Firewall Rules

[video](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/WrQIK/routes-and-rules)

A route is  a mapping of an IP range to a destination.

### Network Billing

[video](https://www.coursera.org/learn/gcp-infrastructure-foundation/lecture/9uFtR/billing)

### Lab 1: Virtual Networking 


Compare and explore a complex GCP network structure.

- you build a complex network topology

<img src="../images/lab_network_diagram.png"
        alt="lab_network_diagram.png"
        style="float: left; margin-right: 10px;" />

- you will launch VMs in varius network/sub-networks

<img src="../images/lab_network_launch_VMs_in_various_networks.png"
        alt="lab_network_launch_VMs_in_various_networks.png"
        style="float: left; margin-right: 10px;" />

- you will ping VMs accross the networks

<img src="../images/lab_network_ping_various_part_of_network.png"
        alt="lab_network_ping_various_part_of_network.png"
        style="float: left; margin-right: 10px;" />


[lab. details](../labs/lab_complx_network_on_GCP.md)



## Module 3: Virtual Machines

In this module, we cover **virtual machine instances**, or **VMs**.

First we'll start with:

- the **basics of Compute Engine**, followed by a quick little lab to get you more familiar with creating virtual machines.
- Then, we’ll look at the **different CPU and memory options** that enable you to create different configurations.
- Next, we will look at images and the **different disk options available with Compute Engine**.
- After that, we will discuss very **common Compute Engine actions** that you might encounter in your day-to-day job.

This will be followed by an in-depth lab that explores many of the features and services covered in this module.




## Resources/Articles

- 
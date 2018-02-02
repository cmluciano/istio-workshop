# Exercise 12 - Addtional features

#### IBM Front Door

IBM Front Door is IBM container service Ingress controller which is an enhancement of the Kubernetes Ingress Controller. The Front Door provides IBM Cloud users with a secure, reliable and scalable high performance network stack to distribute incoming traffic to their applications running on the IBM Cloud Platform. The additional features are simply configured as additional annotations in the yaml file and are deployed when configured. Some features supported by the Front Door Ingress Controller are:
* Unique subdomain with a free certificate
* DNS resolution for the Subdomain
* SSL (TLS) termination
* Layer 7 routing
* Load Balance to an application running on IBM container service
* Load Balance to an external application (specifying external endpoints)
* Highly Available Ingress Controller

In this exercise we'll try out adding domain name to services.


## Workshop Setup
- [Exercise 1 - Accessing a Kubernetes cluster with IBM Cloud Container Service](exercise-1/README.md)

## Exploring Kubernetes

- [Exercise 2 - Deploying a microservice to Kubernetes](exercise-2/README.md)
- [Exercise 3 - Creating a Kubernetes service](exercise-3/README.md)
- [Exercise 4 - Scaling in and out](exercise-4/README.md)

## Creating a Service Mesh with Istio

- [Exercise 5 - Installing Istio](exercise-5/README.md)
- [Exercise 6 - Creating a service mesh with Istio Proxy](exercise-6/README.md)
- [Exercise 7 - Istio Ingress controller](exercise-7/README.md)
- [Exercise 8 - Telemetry](exercise-8/README.md)
- [Exercise 9 - Request routing and canary deployments](exercise-9/README.md)
- [Exercise 10 - Service Isolation](exercise-10/README.md)
- [Exercise 11 - Security](exercise-11/README.md)
- [Exercise 12 - Integrate with Front Door](exercise-12/README.md)


## Credits
These workshop exercises are built with the help from a number of amazing Kubernetes and Istio experts from Google and Grand Cloud.

#### Ray Tsang  [@saturnism](https://twitter.com/saturnism)
The Kubernetes and Istio exercises are derived from the work of Ray Tsang  [@saturnism](https://twitter.com/saturnism) and these repositories:

[https://github.com/saturnism/spring-boot-docker](https://github.com/saturnism/spring-boot-docker)

[https://github.com/saturnism/istio-by-example-java](https://github.com/saturnism/istio-by-example-java)

#### Zach Butcher [@ZachButcher](https://twitter.com/ZackButcher)
Zach was instrumental in helping write the Istio tutorials.

####  Kelsey Hightower [@kelseyhightower](https://twitter.com/kelseyhightower)
The Istio Ingress Tutorial is largely based on the work of Kelsey and this repository:

[https://github.com/kelseyhightower/istio-ingress-tutorial](https://github.com/kelseyhightower/istio-ingress-tutorial)

Kelsey's tutorial uses more advance features of Kubernetes to taint some of the nodes so that the Ingress controller runs on dedicated nodes. The Ingress controller is then deployed as a daemonset.

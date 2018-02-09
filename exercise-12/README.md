# Exercise 12 - Addtional features

### IBM Front Door

IBM Front Door is IBM container service Ingress controller which is an enhancement of the Kubernetes Ingress Controller. The Front Door provides IBM Cloud users with a secure, reliable and scalable high performance network stack to distribute incoming traffic to their applications running on the IBM Cloud Platform. The additional features are simply configured as additional annotations in the yaml file and are deployed when configured. Some features supported by the Front Door Ingress Controller are:
* Unique subdomain with a free certificate
* DNS resolution for the Subdomain
* SSL (TLS) termination
* Layer 7 routing
* Load Balance to an application running on IBM container service
* Load Balance to an external application (specifying external endpoints)
* Highly Available Ingress Controller

In this exercise we'll create an Front Door Ingress and connect it to Istio ingress. And add the domain names to some services.

### Copy secret over
The Front Door ingress will be under the `istio-system` namespace. And the istio ingress created in Lab 6 is under the `default` namespace. To communicate across the namespaces, the secret has to be copied over.
```sh
kubectl get secret -n default
```

```sh
NAME                                   TYPE                                  DATA      AGE
bluemix-default-secret                 kubernetes.io/dockercfg               1         4d
bluemix-default-secret-international   kubernetes.io/dockercfg               1         4d
bluemix-default-secret-regional        kubernetes.io/dockercfg               1         4d
default-token-r2bv7                    kubernetes.io/service-account-token   3         4d
indexistio                             Opaque                                2         4d
istio.default                          istio.io/key-and-cert                 3         2d
```
Pick the secret name showing `Opaque` in `Type`.
Now copy:
```sh
kubectl get secret [secret_name] -o yaml | sed 's/default/istio-system/g' | kubectl -n istio-system create -f -
```
To verify the secret being copied:
```sh
kubectl get secret -n istio-system
```
```sh
NAME                                        TYPE                                  DATA      AGE
...
indexistio                                  Opaque                                2         23s
...
```
### Deploy the Front Door Ingress
In our workshop, we are using `us-ease` region. If you have a cluster from another region, please modify the `guestbook/frontdoor-ingress.yaml` accordingly.

Change the template file to add secret name and cluster name(most likely the same). Then create the Ingress.
```sh
cat guestbook/frontdoor-ingress.yaml| sed 's/xxxx/${cluster_name}/g' | sed 's/ssss/${secret_name}/g' | kubectl -n istio-system create -f -
```
To examine the Ingress, run
```sh
kubectl get ingress istio-fd  -o yaml -n istio-system
```
```sh
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  creationTimestamp: 2018-02-02T23:40:58Z
  generation: 1
  name: istio-fd
  namespace: istio-system
  resourceVersion: "183991"
  selfLink: /apis/extensions/v1beta1/namespaces/istio-system/ingresses/istio-fd
  uid: 84c68584-0872-11e8-a6d2-660c238dff98
spec:
  rules:
  - host: indexistio.us-south.containers.mybluemix.net
    http:
      paths:
      - backend:
          serviceName: istio-ingress
          servicePort: 80
        path: /
  - host: zipkin.indexistio.us-south.containers.mybluemix.net
    http:
      paths:
      - backend:
          serviceName: zipkin
          servicePort: 9411
        path: /
  - host: grafana.indexistio.us-south.containers.mybluemix.net
    http:
      paths:
      - backend:
          serviceName: grafana
          servicePort: 3000
        path: /
  tls:
  - hosts:
    - indexistio.us-south.containers.mybluemix.net
    secretName: indexistio
status:
  loadBalancer: {}
```

Note the part where it connects to istio ingress:
```sh
backend:
   serviceName: istio-ingress
   servicePort: 80
```
Which corresponds to the `guestbook-ui` in istio ingress.   
Now let's access the guestbook service. Try `http://[clustername].us-east.containers.mybluemix.net` and you'll see the guestbook gui.   
And go on with `http://zipkin.[clustername].us-east.containers.mybluemix.net` and `http://grafana.[clustername].us-east.containers.mybluemix.net` to access the zipkin and grafana services.  

Congratulations! You have finished the lab. If you want to find out more about Istio, try out more advanced features, or follow more examples and guides, you can find all this and more at https://istio.io/docs/.


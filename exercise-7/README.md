## Exercise 7 - Istio Ingress controller

The components deployed on the service mesh by default are not exposed outside the cluster. External access to individual services so far has been provided by creating an external load balancer on each service.

A Kubernetes Ingress rule can be created that routes external requests through the Istio Ingress controller to the backing services.

#### Configure Guestbook Ingress routes with the Istio Ingress controller

1. Configure the guestbook UI default route with the Istio Ingress controller.

    ```sh
    kubectl apply -f guestbook/guestbook-ingress.yaml
    ```

    In this file, notice that the Ingress class is specified as `kubernetes.io/ingress.class: istio`, which routes the request to Istio. Also notice that the request is routed to different services, either `helloworld-service` or `guestbook-ui`, depending on the request. You can see how this works by having Kubernetes describe the Ingress resource:

    ```sh
    kubectl describe ingress

    Name:             simple-ingress
    Namespace:        default
    Address:          
    Default backend:  default-http-backend:80 (<none>)
    Rules:
      Host  Path  Backends
      ----  ----  --------
      *     
            /hello/.*   helloworld-service:8080 (<none>)
            /.*         guestbook-ui:80 (<none>)
    Annotations:
    Events:  <none>
    ```

2. Get the external IP of the Istio Ingress controller.

    ```sh
    kubectl get service istio-ingress -n istio-system

    NAME                   CLUSTER-IP      EXTERNAL-IP      PORT(S)                       AGE
    istio-ingress          10.31.244.185   169.47.103.138   80:31920/TCP,443:32165/TCP    1h
    ```

3. Export the external IP address from the previous command.
   
    ```sh
    INGRESS_IP=$(kubectl get ing | grep simple-ingress | awk '{ print $3 }')
    ```

4. Use the INGRESS IP to see the guestbook UI in a browser: `http://INGRESS_IP`. You can also access the Hello World service and see the JSON in the browser: `http://INGRESS_IP/hello/world`.


5. Curl the guestbook:
    ```
    curl http://$INGRESS_IP/echo/universe
    ```

6. Curl the Hello World service:
    ```
    curl http://$INGRESS_IP/hello/world
    ```

7. Then curl the echo endpoint multiple times and notice how it round robins between v1 and v2 of the Hello World service:

    ```sh
    curl http://$INGRESS_IP/echo/universe

    {"greeting":{"hostname":"helloworld-service-v1-286408581-9204h","greeting":"Hello universe from helloworld-service-v1-286408581-9204h with 1.0","version":"1.0"},
    ```

    ```sh
    curl http://$INGRESS_IP/echo/universe

    {"greeting":{"hostname":"helloworld-service-v2-1009285752-n2tpb","greeting":"Hello universe from helloworld-service-v2-1009285752-n2tpb with 2.0","version":"2.0"}

    ```

#### [Continue to Exercise 8 - Telemetry](../exercise-8/README.md)

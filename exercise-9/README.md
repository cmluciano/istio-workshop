# Exercise 9 - Request routing and canary deployments

Before modifying any of the routes, a default route must be set to just `v1` of the Hello World service deployment. Otherwise, the round robin between `v1` and `v2` of the service deployment will continue.

### Configure the default route for the Hello World service

1. Set the default version for all requests to the Hello World service.

    ```sh
    istioctl create -f guestbook/route-rule-force-hello-v1.yaml
    ```

    This Ingress rule forces `v1` of the service by giving it a weight of 100. We can see this by describing the resource we created:
    
    ```sh
    kubectl describe routerules helloworld-default

    Name:         helloworld-default
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>
    API Version:  config.istio.io/v1alpha2
    Kind:         RouteRule
    Metadata:
      ...
    Spec:
      Destination:
        Name:      helloworld-service
      Precedence:  1
      Route:
        Labels:
          Version:  1.0
    ```

2. Now when you curl the echo service, it should always return `v1` of the Hello World service.

    ```sh
    curl http://$INGRESS_IP/echo/universe  

    {"greeting":{"hostname":"helloworld-service-v1-286408581-9204h","greeting":"Hello universe from helloworld-service-v1-286408581-9204h with 1.0","version":"1.0"},"
    ```
    
    ```sh
    curl http://$INGRESS_IP/echo/universe

    {"greeting":{"hostname":"helloworld-service-v1-286408581-9204h","greeting":"Hello universe from helloworld-service-v1-286408581-9204h with 1.0","version":"1.0"},"
    ```

### Canary Deployments

Currently the routing rule only routes to `v1` of the Hello World service. What we want to do is canary a deployment of `v2` of the Hello World service by allowing only a small amount of traffic to it. This can be done by creating another rule with a higher precedence that routes some of the traffic to `v2`. We'll canary based on HTTP headers: if the user-agent is "mobile", it will go to `v2`; otherwise, requests will go to `v1`. Written as a route rule, this looks like:

```yaml
destination:
  name: helloworld-service
match:
  request:
    headers:
      user-agent:
        exact: mobile
precedence: 2
route:
  - labels:
      version: "2.0"
```

Note that rules with a higher precedence number are applied first. If a precedence is not specified, then it defaults to 0. So with these two rules in place, the one with precedence 2 will be applied before the rule with precedence 1.

1. Test this out by creating the canary rule.

    ```sh
    istioctl create -f guestbook/route-rule-canary.yaml
    ```

2. Now when you curl the end point, set the user agent to be mobile. You should only see `v2`.

    ```sh
    curl http://$INGRESS_IP/echo/universe -A mobile

    {"greeting":{"hostname":"helloworld-service-v2-3297856697-6m4bp","greeting":"Hola world from helloworld-service-v2-1131997838-qnwcm version 2.0","version":"2.0"}
    ```
    
    Without the user agent being set to mobile, you should still only see `v1`:
    
    ```sh
    $ curl http://$INGRESS_IP/echo/universe

    {"greeting":{"hostname":"helloworld-service-v1-286408581-9204h","greeting":"Hello universe from helloworld-service-v1-286408581-9204h with 1.0","version":"1.0"},"
    ```

Note that the user-agent HTTP header is propagated in the span baggage. Check out these two classes for details on how the header is [injected](https://github.com/retroryan/istio-by-example-java/blob/master/spring-boot-example/spring-istio-support/src/main/java/com/example/istio/IstioHttpSpanInjector.java) and [extracted](https://github.com/retroryan/istio-by-example-java/blob/master/spring-boot-example/spring-istio-support/src/main/java/com/example/istio/IstioHttpSpanExtractor.java).

### Optional - Route based on the browser

It is also possible to route based on the web browser used. For example, the following rule routes to `v2` if the browser is Chrome:

```yaml
match:
  request:
    headers:
      user-agent:
        regex: ".*Chrome.*"
route:
- labels:
    version: "2.0"
```

1. To apply this route rule, run:

    ```sh
    istioctl create -f guestbook/route-rule-user-agent-chrome.yaml
    ```

2. Navigate to `http://$INGRESS_IP/echo/universe` in Chrome. You should see:
    
    ```
    Hola test from helloworld-service-v2-87744028-x20j0 version 2.0
    ```

3. Navigate to `http://$INGRESS_IP/echo/universe` in another browser. You should see:

    ```
    Hello sdsdffsd from helloworld-service-v1-4086392344-42q21 with 1.0
    ```

#### [Continue to Exercise 10 - Fault injection and rate limiting](../exercise-10/README.md)

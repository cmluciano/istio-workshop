# Exercise 10 - Fault injection and rate limiting

#### Overview of Istio Mixer

See the overview of Mixer at [istio.io](https://istio.io/docs/concepts/policy-and-control/mixer.html).

### Service isolation using Mixer

1. Block access to the Hello World service.

    ```sh
    istioctl create -f guestbook/mixer-rule-denial.yaml
    ```

    This creates the `mixer-rule-denial.yaml` rule:

    ```yaml
    # Create a denier that returns a google.rpc.Code 7 (PERMISSION_DENIED)
    apiVersion: "config.istio.io/v1alpha2"
    kind: denier
    metadata:
      name: denyall
      namespace: istio-system
    spec:
      status:
        code: 7
        message: Not allowed
    ---
    # The (empty) data handed to denyall at run time
    apiVersion: "config.istio.io/v1alpha2"
    kind: checknothing
    metadata:
      name: denyrequest
      namespace: istio-system
    spec:
    ---
    # The rule that uses denier to deny requests to the helloworld service
    apiVersion: "config.istio.io/v1alpha2"
    kind: rule
    metadata:
      name: deny-hello-world
      namespace: istio-system
    spec:
      match: destination.service=="helloworld-service.default.svc.cluster.local"
      actions:
      - handler: denyall.denier
        instances:
        - denyrequest.checknothing
    ```

2. Verify that access is now denied.
    
    ```sh
    curl http://$INGRESS_IP/hello/world
    ```

3. Clean up the rule.
    
    ```sh
    istioctl delete -f guestbook/mixer-rule-denial.yaml
    ```

### Block access to v2 of the Hello World service
 
1. Block access to only v2 of the Hello World service deployment.

    ```sh
    istioctl create -f guestbook/mixer-rule-denial-v2.yaml
    ```

    This creates the `mixer-rule-denial-v2.yaml` rule:

    ```yaml
    # The rule that uses denier to deny requests to version 2.0 of the helloworld service
    apiVersion: "config.istio.io/v1alpha2"
    kind: rule
    metadata:
      name: deny-hello-world
      namespace: istio-system
    spec:
      match: destination.service=="helloworld-service.default.svc.cluster.local" && destination.labels["version"] == "2.0"
      actions:
      - handler: denyall.denier
        instances:
        - denyrequest.checknothing
    ```

2. Verify that you can access the v1 service:

    ```sh
    curl http://$INGRESS_IP/hello/world
    ```

3. Verify that access to v2 is denied:
   
    ```sh
    curl http://$INGRESS_IP/hello/world -A mobile
    ```

4. Clean up the rule.

    ```sh
    istioctl delete -f guestbook/mixer-rule-denial-v2.yaml
    ```
#### [Continue to Exercise 11 - Security](../exercise-11/README.md)

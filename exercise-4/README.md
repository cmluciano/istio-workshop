# Exercise 4 - Scaling in and out

### Scale the number of Hello World service pods

1. Scale the number of replicas of your Hello World service by running the following commands:

    ```sh
    kubectl get deployment

    NAME                    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    helloworld-service-v1   1         1         1            1           1m
    ```

    ```sh
    kubectl scale deployment helloworld-service-v1 --replicas=4
    ```

    ```sh
    kubectl get deployment

    NAME                    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    helloworld-service-v1   4         4         4            4           1m
    ```

    ```sh
    kubectl get pods

    NAME                          READY     STATUS    RESTARTS   AGE
    helloworld-service-v1-...    1/1       Running   0          1m
    helloworld-service-v1-...    1/1       Running   0          1m
    helloworld-service-v1-...    1/1       Running   0          1m
    helloworld-service-v1-...    1/1       Running   0          2m
    ```

2. Try scaling out further.

    ```
    kubectl scale deployment helloworld-service-v1 --replicas=17
    ```

If you look at the pod status, some of the pods will show a `Pending` state. That is because we have cordoned one worker node, leaving only two available for scheduling. And the underlying infrastructure has run out of capacity to run the containers with the requested resources.

3. Pick a pod name that has a `Pending` state to confirm the lack of resources in the detailed status.

    ```
    kubectl describe pod helloworld-service...
    ```

4. Uncordon the worker to be available for scheduling.

    a. Open another terminal, do the `export KUBECONFIG ...` command in [Lab 1](../exercise-1/README.md) and run:
        
        ```
        kubectl get po -w -o wide
        ```
        This will monitor the recovering process.
    
    b. Go back the working terminal. Get the name of the worker that has status "not-ready".
       
        ```
        kubectl get nodes
        ```
    
    c.  Undordon the worker to make it available for scheduling.
        
        ```
        kubectl uncordon [name]
        ```
        
5. Verify all three workers are available and the pending pod is rescheduled.     

    ```
    kubectl get nodes
    kubectl get pods -o wide
    ```

### Clean up
 
Start with a clean slate by deleting all deployed services from the cluster.

```sh
kubectl delete all --all
```

#### [Continue to Exercise 5 - Installing Istio](../exercise-5/README.md)

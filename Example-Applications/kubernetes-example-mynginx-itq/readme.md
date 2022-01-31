
## Example application with a Persistant Volume

### If not already existing, create a namespace 

```kubectl create namespace test-example-namespace```

### Verify there is nothing in the namespace

### Create the PVC: 

```kubectl apply -f kubernetes-example-nginx-test-pvc.yml --namespace test-example-namespace```

this will create PVC of 5gb

### Check creation of PVC and PV

```kubectl get all,pvc,pv -n test-example-namespace``` 

### Create the pod and a service of type LB

```kubectl apply -f ./kubernetes-example-nginx-test-pod.yml -f ./kubernetes-example-nginx-test-service.yml --namespace test-example-namespace```

### Look inside te container:

```kubectl exec --stdin --tty kubernetes-example-nginx-test /bin/bash --namespace test-example-namespace```

Inside the container run ```df -h``` to verify the PVC is mounted. The way we verify that it is mounted is by checking that there is a 5Gb directory on /usr/share/nginx/html

### exit the container's shell

```exit``` 

### lookup the service: LB external IP address, and check in the browser if the app functions and shows the default nginx 403 'forbidden'page


```kubectl get svc --namespace test-example-namespace``` 

### Copy a custom HTML index file into the container, into the mount point of the PVC (and thus; into the PV)

```kubectl cp build-test-custom-image/static-html-directory/index.html kubernetes-example-nginx-test:/usr/share/nginx/html --namespace test-example-namespace -v=7```

Refresh the page in the browser, it should now show our custom HTML page. 
This page is saved in the Persistant Volume. As long a the persistant volume remains, this page will be there


### Remove the pod and service, but leave the PV


```kubectl delete -f ./kubernetes-example-nginx-test-pod.yml -f ./kubernetes-example-nginx-test-service.yml --namespace test-example-namespace```


### Remove the entire namespace, including pod, service and PV

```kubectl delete namespace test-example-namespace```

## Add a custom HTML index file to a default nginx container

build the container:

```docker build -t registry.kubernetes.mycompany.nl/library/nginx-test:v1 .```

```
Sending build context to Docker daemon  3.584kB
Step 1/2 : FROM registry.kubernetes.mycompany.nl/library/nginx
 ---> 6084105296a9
Step 2/2 : COPY static-html-directory /usr/share/nginx/html
 ---> 8c2eb26137ee
Successfully built 8c2eb26137ee
Successfully tagged registry.kubernetes.mycompany.nl/library/nginx-test:v1

```

push the container to harbor:

```docker push registry.kubernetes.mycompany.nl/library/nginx-test:v1```




Look inside te container:

```kubectl exec --stdin --tty kubernetes-example-nginx-test-- /bin/bash```


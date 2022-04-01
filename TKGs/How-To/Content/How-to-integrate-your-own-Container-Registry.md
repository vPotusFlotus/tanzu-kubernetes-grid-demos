# How to integrate your own Container Registry

You can integrate your existing Container Repository with Tanzu Kubernetes Clusters. It's important to point out that currently you can use such an External Container Registry with Tanzu Kubernetes Cluster pods only; it's not compatible with vSphere Pods. 

The Official VMware Documentation can be found here:
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-376FCCD1-7743-4202-ACCA-56F214B6892F.html

Basically we are just going to trust your External Container Registry's SSL Certificate in the Tanzu Kubernetes Cluster(s).

## Table of Contents
1. [Prerequisites](#prerequisites)
1. [Trust your External Container Registry](#trust-your-external-container-registry)
1. [Deploy Workload from your External Container Registry](#deploy-workload-from-your-external-container-registry)

## Prerequisites
* [Make sure you are logged in](How-to-Login.md) to the Supervisor Cluster. 

## Trust your External Container Registry

This can be achieved by craeting a TkgServiceConfiguration Specification. 

A detailed procedure is outlined here:
1. [How to integrate External Container Registry](https://vra4u.com/2022/03/16/tkgs-quick-tip-how-to-integrate-external-container-registry-in-vsphere-with-tanzu/)
1. If you want an existing Tanzu Kubernetes Cluster to trust your External Container Registry, you can scale your TKC to trigger a rolling update. To find out how, see our article [here](How-to-Scale-an-existing-Tanzu-Kubernetes-Cluster.md). 

## Deploy Workload from your External Container Registry

In our example we had 1 TKC running and Scaled this Cluster out with 1 extra Worker Node. This triggered a Rolling Update and now our TKC trusts our External Container Registry. 

Let's test it out!

1. Create a new YAML Specification that points to a Container Image in your External Container Registry. 
We have a Nexus Repository with a SpaceInvaders image in so our YAML looks like this:
    ````
    vi localrepo-nginx-spaceinvaders.yaml

    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: spaceinvaders-localrepo
    spec:
      strategy:
        type: Recreate
      selector:
        matchLabels:
          app: spaceinvaders-localrepo
      replicas: 1 # tells deployment to run 3 pods matching    e template
      template: # create pods using pod definition in this    mplate
        metadata:
          labels:
            app: spaceinvaders-localrepo
        spec:
          containers:
          - name: spaceinvaders-localrepo
            image: 10.10.60.15:5000/spaceinvadershtml5:latest
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: spaceinvaders-lb-localrepo
      namespace: default
      labels:
        app: spaceinvaders-lb-localrepo
    spec:
      externalTrafficPolicy: Local
      ports:
      - name: http
        port: 8080
        protocol: TCP
        targetPort: 80
      selector:
        app: spaceinvaders-localrepo
      type: LoadBalancer
    ````
As you can see in our example we are pulling an image from our Container Registry on '10.10.60.15'. We made sure to add '-localrepo' in the names and labels of this Local Repo Application Deployment. 
1. Deploy your Local Repo Application:
    ````
    kubectl apply -f localrepo-nginx-spaceinvaders.yaml 

    deployment.apps/spaceinvaders-localrepo created
    service/spaceinvaders-lb-localrepo created
    ````
1. To see if your TKC was able to pull an image from your Container Registry, run 'kubectl describe' on your Pod as and look to the 'Events' section as shown below:
    ````
    kubectl describe po spaceinvaders-localrepo-86cf45db4b-mvw6g

    Name:         spaceinvaders-localrepo-86cf45db4b-mvw6g
    Namespace:    default
    Priority:     0
    Node:           tkgs-v2-wl03-worker-nodepool-b1-zwhzx-758c499ccb-lns5x/10.10. 70.177
    Start Time:   Thu, 31 Mar 2022 20:23:57 +0200
    Labels:       app=spaceinvaders-localrepo
                  pod-template-hash=86cf45db4b
    ......
    Events:
    Type    Reason     Age   From               Message
    ----    ------     ----  ----               -------
    Normal  Scheduled  36s   default-scheduler  Successfully  assigned default/spaceinvaders-localrepo-86cf45db4b-mvw6g    to tkgs-v2-wl03-worker-nodepool-b1-zwhzx-758c499ccb-lns5x
    Normal  Pulling    35s   kubelet            Pulling image     "10.10.60.15:5000/spaceinvadershtml5:latest"
    Normal  Pulled     14s   kubelet            Successfully  pulled image "10.10.60.15:5000/spaceinvadershtml5:latest"    in 20.994836664s
    Normal  Created    14s   kubelet            Created   container spaceinvaders-localrepo
    Normal  Started    14s   kubelet            Started   container spaceinvaders-localrepo
    ````
Seems that our TKC was successfully able to pull an image from our Container Registry! 



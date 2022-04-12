# How to Login

VMware has extended the functionality of the 'kubectl' command with a vSphere integration. This vSphere integration allows you to login with your native 'kubectl' command on vSphere with Tanzu Namespaces and Tanzu Kubernetes Clusters for example. 

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Kubectl vSphere Login Options](#kubectl-vsphere-login-options)
3. [Connect to the Supervisor Cluster](#connect-to-the-supervisor-cluster)
4. [Connect to a specific vSphere Namespace](#connect-to-a-specific-vsphere-namespace)
5. [Connect to a specific Tanzu Kubernetes Cluster in a certain vSphere Namespace](#connect-to-a-specific-tanzu-kubernetes-cluster-in-a-certain-vsphere-namespace)
6. [Ignore Certificate warnings](#ignore-certificate-warnings)
7. [Check your Kubectl Context](#check-your-kubectl-context)

## Prerequisites

* Make sure to [install the Kubernetes CLI Tools for vSphere](Install-Kubernetes-CLI-Tools-for-vSphere.md)

## Kubectl vSphere Login Options

Logging in to vSphere with Tanzu happens with the 'kubectl vsphere login' command. 

Run the following command to list the available options:

```
kubectl vsphere login -h
```

This should provide the following output:

```
Authenticate user with vCenter Namespaces:
To access Kubernetes, you must first authenticate against vCenter Namespaces.
You must pass the address of the vCenter Namspaces server and the username of
the user to authenticate, and will be prompted to provide further credentials.

Usage:
  kubectl-vsphere login [flags]

Examples:
kubectl vsphere login --vsphere-username user@domain --server=https://10.0.1.10

Flags:
  -h, --help                                        help for login
      --insecure-skip-tls-verify                    Skip certificate verification (this is insecure).
      --server string                               Address of the server to authenticate against.
      --tanzu-kubernetes-cluster-name string        Name of the Tanzu Kubernetes cluster to login to.
      --tanzu-kubernetes-cluster-namespace string   Namespace in which the Tanzu Kubernetes cluster resides.
  -u, --vsphere-username string                     Username to authenticate.

Global Flags:
      --request-timeout string   Request timeout for HTTP client.
  -v, --verbose int              Print verbose logging information.

```

## Connect to the Supervisor Cluster

You can simply authenticate to the Supervisor cluster byy providing the Control Plane IP Address and the vCenter SSO Username. This vCenter SSO Username can be from any domain (local, AD, ...) that you integrated with vCenter and that has the necessary access rights. 

```
kubectl vsphere login --server=<KUBERNETES-CONTROL-PLANE-IP-ADDRESS> --vsphere-username <VCENTER-SSO-USER>
```

Example:
```
kubectl vsphere login --server=10.10.70.11 --vsphere-username=administrator@vsphere.local
```

## Connect to a specific vSphere Namespace

In order to authenticate in a certain vSphere Namespace, just provide the option '--tanzu-kubernetes-cluster-namespace' to your login command as shown below. 

```
kubectl vsphere login --server=<KUBERNETES-CONTROL-PLANE-IP-ADDRESS> --vsphere-username <VCENTER-SSO-USER> --tanzu-kubernetes-cluster-namespace=<NAMESPACE>
```

Example:
```
kubectl vsphere login --server=10.10.70.11 --vsphere-username=administrator@vsphere.local --tanzu-kubernetes-cluster-namespace=dev
```

## Connect to a specific Tanzu Kubernetes Cluster in a certain vSphere Namespace

If you want to connect to a specific TKC in a certain vSphere Namespace, just provide your TKC Name additionally to your login command. 

```
kubectl vsphere login --server=<KUBERNETES-CONTROL-PLANE-IP-ADDRESS> --vsphere-username <VCENTER-SSO-USER> --tanzu-kubernetes-cluster-namespace=<NAMESPACE> --tanzu-kubernetes-cluster-name=<TANZU-CLUSTER-NAME>
```

Example:
```
kubectl vsphere login --server=10.10.70.11 --vsphere-username=administrator@vsphere.local --tanzu-kubernetes-cluster-namespace=dev --tanzu-kubernetes-cluster-name=tkgs-v2-wl02
```

## Ignore Certificate warnings

If your local machine does not trust the certificate of the Control Plane, you can use the '--insecure-skip-tls-verify' flag to ignore any Certificate Warnings. Use with caution!

```
kubectl vsphere login --server=<KUBERNETES-CONTROL-PLANE-IP-ADDRESS> --vsphere-username <VCENTER-SSO-USER> --tanzu-kubernetes-cluster-namespace=<NAMESPACE> --tanzu-kubernetes-cluster-name=<TANZU-CLUSTER-NAME> 
--insecure-skip-tls-verify 
```

Example:
```
kubectl vsphere login --server=10.10.70.11 --vsphere-username=administrator@vsphere.local --tanzu-kubernetes-cluster-namespace=dev --tanzu-kubernetes-cluster-name=tkgs-v2-wl02 --insecure-skip-tls-verify
```

## Check your Kubectl Context

After logging in with the 'kubectl vsphere login' command, you will either be logged in to:
* The Supervisor Cluster
* Your vSphere Namespace
* Your Tanzu Kubernetes Cluster in a certain vSphere Namespace

This is facilitated by the native 'contexts' functionality from 'kubectl'. 

To check your current context, just run the following command:

````
kubectl config get-contexts
````

The context marked with a '*' is your active context. 

To change your context:
````
kubectl config use-context <CONTEXT-NAME>
````

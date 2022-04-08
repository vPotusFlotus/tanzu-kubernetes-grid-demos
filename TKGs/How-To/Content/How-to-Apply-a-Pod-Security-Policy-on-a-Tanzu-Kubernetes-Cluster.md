# How to Apply a Pod Security Policy on a Tanzu Kubernetes Cluster

Tanzu Kubernetes Clusters are provisioned with the PodSecurityPolicy Admission Controller enabled. This means that a Pod Security Policy is required to deploy workloads. 

The Official VMware Documentation can be found here:
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-CD033D1D-BAD2-41C4-A46F-647A560BAEAB.html

By default a TKC comes with 2 Pod Security Policies:
* **vmware-system-privileged**  -- Run as Any -- Equivalant to running a cluster without the PSP Admission Controller Enabled
* **vmware-system-restricted** -- Must run as non-root -- Does not permit privileged access to Pod Containers

A TKC does not have a default RoleBinding and ClusterRoleBinding set to the default Pod Security Policies.

A Tanzu Kubernetes Cluster Administrator needs to create RoleBindings & ClusterRoleBindings first in order to govern how Cluster Users can deploy workloads on Tanzu Kubernetes Clusters.

Tanzu Kubernetes Cluster Administrators are vCenter SSO Users who are assigned the **edit** permission in their respective vSphere Namespace. 

There are 2 ways to create Workloads on your TKC:
* You create a Pod directly by deploying a Pod Specification with your User Account. In this case your User Account needs the necessary privileges to do this. 
* You create a Pod indirectly by defining some higher-level resource, such as a Deployment or DaemonSet. In this case a Service Account creates the underlying Pod. 

The Official VMware Documented examples can be found here:
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-4CCDBB85-2770-4FB8-BF0E-5146B45C9543.html

## Table of Contents
1. [Prerequisites](#prerequisites)
1. [Apply a Restrictive Pod Security Policy to Authenticated Users Cluster Wide](#apply-a-restrictive-pod-security-policy-to-authenticated-users-cluster-wide) 
1. [Apply a Permissive Pod Security Policy to Service Accounts in the Default Namespace](#apply-a-permissive-pod-security-policy-to-service-accounts-in-the-default-namespace)
1. [Apply a Restrictive Pod Security Policy to Service Accounts in the Default Namespace](#apply-a-restrictive-pod-security-policy-to-service-accounts-in-the-default-namespace)

## Prerequisites
* [Make sure you are logged in](How-to-Login.md) and connected to the Tanzu Kubernetes Cluster in the corresponding vSphere Namespace
* Make sure you have at least the **edit** role in that specific vSphere Namespace so you are a Tanzu Kubernetes Cluster Administrator

## Apply a Restrictive Pod Security Policy to Authenticated Users Cluster Wide

In our example we are going to leverage the following Default PodSecurityPolicy (PSP):
* vmware-system-restricted

We will assign this PSP to Authenticated Users Cluster Wide; this means that our Authenticated Users can only provision non-privileged workloads. 

Let's start by creating a ClusterRoleBinding YAML:
````
vi clusterrolebinding-restrictive.yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp:authenticated
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:vmware-system-restricted

kubectl apply -f clusterrolebinding-restrictive.yaml

clusterrolebinding.rbac.authorization.k8s.io/psp:authenticated created
````

## Apply a Permissive Pod Security Policy to Service Accounts in the Default Namespace

In our example we are going to leverage the following Default PodSecurityPolicy (PSP):
* vmware-system-privileged

We will assign this PSP to Service Accounts in the Default Namespace; this means that our Service Accounts can provision privileged workloads in the Default Namespace. 

````
vi defaultnamespace-permissive.yaml

kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: rolebinding-default-privileged-sa-ns_default
  namespace: default
roleRef:
  kind: ClusterRole
  name: psp:vmware-system-privileged
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: Group
  apiGroup: rbac.authorization.k8s.io
  name: system:serviceaccounts

kubectl apply -f defaultnamespace-permissive.yaml

rolebinding.rbac.authorization.k8s.io/rolebinding-default-privileged-sa-ns_default created
````

## Apply a Restrictive Pod Security Policy to Service Accounts in the Default Namespace

In our example we are going to leverage the following Default PodSecurityPolicy (PSP):
* vmware-system-restricted

We will assign this PSP to Service Accounts in the Default Namespace; this means that our Service Accounts can only provision non-privileged workloads in the Default Namespace.

````
vi defaultnamespace-restrictive.yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: psp:serviceaccounts
  namespace: default
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:vmware-system-restricted

kubectl apply -f defaultnamespace-restrictive.yaml

rolebinding.rbac.authorization.k8s.io/psp:serviceaccounts created
````

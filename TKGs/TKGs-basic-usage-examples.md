# TKGs Basic Usage Examples and Explanations

This file is meant for the following purposes: 

* to collect examples of typical TKGs usage examples, uncluding example one-liners. This of commone day-2 administrative tasks and activities. 

* to expand and explain on specific topics or concepts related to TKGs use

It should be viewed as an addendum, or a more compact and easy to share version of the activities that are documented by VMware in the official docs here: https://docs.vmware.com/en/VMware-vSphere/index.html

We can add to this anything we deem of value that goes beyond VMware's basic documentation. 



# TKGS Roles

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-70CAF0BB-1722-4526-9CE7-D5C92C15D7D0.html
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-A6EFA324-7C58-4DB4-9166-CE18462A8CCF.html

## vSphere Administrator


As a vSphere administrator, you create a vSphere Namespace on the Supervisor Cluster. 
You set resources limits to the namespace and permissions so that DevOps engineers can access it. 
You provide the URL of the Kubernetes control plane to DevOps engineers where they can run Kubernetes workloads on the namespaces for which they have permissions

As a vSphere administrator, you can manage and monitor vSphere Pods, VMs, and Tanzu Kubernetes clusters by using the vSphere Client.

As a vSphere administrator, you have full visibility over vSphere Pods, VMs, and Tanzu Kubernetes clusters running within different namespaces, 
their placement in the environment, and how they consume resources.




## DevOps Engineer

As a DevOps engineer, you create and configure Tanzu Kubernetes clusters on a namespace created and configured by your vSphere administrator.
As a DevOps engineer, you can run workloads consisting of Kubernetes containers on the same platform with shared resource pools within aSupervisor Namespace. 
As a DevOps engineer, you can deploy vSphere Pods and VMs within the resources boundaries of a namespace that is running on a Supervisor Cluster.
As a DevOps engineer, you can create and manage multiple Kubernetes clusters inside a namespace and manage their lifecycle by using the Tanzu Kubernetes Grid Service. 
Kubernetes clusters created by using the Tanzu Kubernetes Grid Service are called Tanzu Kubernetes clusters.

Give rights to ALL namespaces?
Give right to SPECIFIC namespaces? 





## Cluster Administrator

A Cluster Administrator role for a specific Kubernetes cluster, could be held by the DevOps engineer for the namespace that the cluster was created in, or by specific developers, or both. 
By default, and user granted the Edit permission on the Supervisor Namespace where the cluster is deployed is automatically assigned to the cluster-admin role. 
This would usually be only the vSphere Administrators, or the DevOps engineer for that namespace.

Cluster administrators connect to a provisioned Tanzu Kubernetes cluster to operate and manage it.

Cluster administrators authenticate using the vSphere Plugin for kubectl and their vCenter Single Sign-On credentials. 

Alternatively, cluster administrators can connect to a Tanzu Kubernetes cluster as the kubernetes-admin user. 
This method might be appropriate if vCenter Single Sign-On authentication is not available. 



## Developer

As an application developer, you can run Kubernetes pods, and deploy and manage Kubernetes based applications. 
You do not have visibility over the entire stack that is running hundreds of applications.

Cluster users or developers connect to a Tanzu Kubernetes cluster to deploy workloads, including pods, services, load balancers, and other resources.
A cluster administrator grants cluster access to developers by binding the user or group to default or custom pod security policy. 
Bound developers authenticate with Tanzu Kubernetes clusters using the vSphere Plugin for kubectl and their vCenter Single Sign-On credentials. 
vSphere Plugin for kubectl automatically provisions their Kubectl config with the appropriate access tokens for the clusters they have rights in. 


# vSphere Namespace Roles

Possible Namespace Rights:

## View  

(does this automaticlaly bind the 'view' clusterrole? )


## Edit


Edit permissions on the namespace will automatically grand Kubernetes cluster-admin role in any clusters that are created under that namespace
 
Anyone with Edit permission on the Namespace, has cluster-admin access to ANY k8s cluster made IN that namespace

## No role on the namespace (in vSphere)

To grant a non-devops engineer ANY access to a k8s cluster, must always be done via a clusterrolebinding
These users, do not requires ANY rights on the Namespace itself. 



# vSphere Namespace Management Tasks

## Manage VirtualMachineClasses

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-7351EEFF-4EF0-468F-A19B-6CEA40983D3D.html

This can be done at the supervisor or namespace contexts
kubectl get virtualmachineclasses
kubectl describe virtualmachineclasses

The command kubectl get virtualmachineclasses lists all the VM classes present on the Supervisor Cluster. Because you must associate VM classes with the vSphere Namespace, you can only use those VM classes that are bound to the target namespace.

```
kubectl get virtualmachineclasses

NAME                  AGE
best-effort-2xlarge   2d5h
best-effort-4xlarge   2d5h
best-effort-8xlarge   2d5h
best-effort-large     2d5h
best-effort-medium    2d5h
best-effort-small     2d5h
best-effort-xlarge    2d5h
best-effort-xsmall    2d5h
guaranteed-2xlarge    2d5h
guaranteed-4xlarge    2d5h
guaranteed-8xlarge    2d5h
guaranteed-large      2d5h
guaranteed-medium     2d5h
guaranteed-small      2d5h
guaranteed-xlarge     2d5h
guaranteed-xsmall     2d5h
```

View the details of a single class:

```
kubectl describe virtualmachineclass best-effort-medium

Name:         best-effort-medium
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  vmoperator.vmware.com/v1alpha1
Kind:         VirtualMachineClass
Metadata:
  Creation Timestamp:  2021-05-03T13:10:11Z
  Generation:          1
  Managed Fields:
    API Version:  vmoperator.vmware.com/v1alpha1
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:hardware:
          .:
          f:cpus:
          f:memory:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2021-05-03T13:10:11Z
  Resource Version:  2505
  Self Link:         /apis/vmoperator.vmware.com/v1alpha1/virtualmachineclasses/best-effort-medium
  UID:               de1d8aec-bd48-46ad-9e38-3de86b3ce6b6
Spec:
  Hardware:
    Cpus:    2
    Memory:  8Gi
Events:      <none>
```

## Manage VirtualMachineBindings 
(not fully functional prior to vSphere7 U2a)


To use a virtual machine class with a Tanzu Kubernetes cluster, the VM class must be bound to the vSphere Namespace where the cluster is provisioned. To do this, you associate the class with the target namespace. 

There are 2 sources of bound Virtual Machines Classes: 
- Associate the Content Library with the vSphere Namespace
- Associate the VM Classes with the vSphere Namespace  (this is part of the 'VM Service instroduced in vSsphere7U2a and is not in scope of this PoC)


To list the VM classes available in the target vSphere Namespace, use the command kubectl get virtualmachineclassbinding. To view all virtual machine classes present on the Supervisor Cluster, run the command kubectl describe virtualmachineclasses. Note, however, that because only bound classes can be used to provision a cluster, the latter command is informational-only. See Workflow for Provisioning Tanzu Kubernetes Clusters.

The command kubectl get virtualmachineclassbindings , does not work prior to vSphere 7U2a, as the VMService Operator is not yet available. Trying to use this command results in an error: 

```
kubectl get virtualmachineclassbindings

Error from server (Forbidden): virtualmachineclassbindings.vmoperator.vmware.com is forbidden: User "sso:rkloosterhuis.admin@resource.intra" cannot list resource "virtualmachineclassbindings" in API group "vmoperator.vmware.com" in the namespace "default"
```

## Manage Virtual Machines Images from the content Library

```
kubectl get virtualmachineimages

NAME                                                         VERSION                           OSTYPE                FORMAT
ob-15957779-photon-3-k8s-v1.16.8---vmware.1-tkg.3.60d2ffd    v1.16.8+vmware.1-tkg.3.60d2ffd    vmwarePhoton64Guest   ovf
ob-16466772-photon-3-k8s-v1.17.7---vmware.1-tkg.1.154236c    v1.17.7+vmware.1-tkg.1.154236c    vmwarePhoton64Guest   ovf
ob-16545581-photon-3-k8s-v1.16.12---vmware.1-tkg.1.da7afe7   v1.16.12+vmware.1-tkg.1.da7afe7   vmwarePhoton64Guest   ovf
ob-16551547-photon-3-k8s-v1.17.8---vmware.1-tkg.1.5417466    v1.17.8+vmware.1-tkg.1.5417466    vmwarePhoton64Guest   ovf
ob-16897056-photon-3-k8s-v1.16.14---vmware.1-tkg.1.ada4837   v1.16.14+vmware.1-tkg.1.ada4837   vmwarePhoton64Guest   ovf
ob-16924026-photon-3-k8s-v1.18.5---vmware.1-tkg.1.c40d30d    v1.18.5+vmware.1-tkg.1.c40d30d    vmwarePhoton64Guest   ovf
ob-16924027-photon-3-k8s-v1.17.11---vmware.1-tkg.1.15f1e18   v1.17.11+vmware.1-tkg.1.15f1e18   vmwarePhoton64Guest   ovf
ob-17010758-photon-3-k8s-v1.17.11---vmware.1-tkg.2.ad3d374   v1.17.11+vmware.1-tkg.2.ad3d374   vmwarePhoton64Guest   ovf
ob-17332787-photon-3-k8s-v1.17.13---vmware.1-tkg.2.2c133ed   v1.17.13+vmware.1-tkg.2.2c133ed   vmwarePhoton64Guest   ovf
ob-17419070-photon-3-k8s-v1.18.10---vmware.1-tkg.1.3a6cd48   v1.18.10+vmware.1-tkg.1.3a6cd48   vmwarePhoton64Guest   ovf
ob-17654937-photon-3-k8s-v1.18.15---vmware.1-tkg.1.600e412   v1.18.15+vmware.1-tkg.1.600e412   vmwarePhoton64Guest   ovf
ob-17658793-photon-3-k8s-v1.17.17---vmware.1-tkg.1.d44d45a   v1.17.17+vmware.1-tkg.1.d44d45a   vmwarePhoton64Guest   ovf
ob-17660956-photon-3-k8s-v1.19.7---vmware.1-tkg.1.fc82c41    v1.19.7+vmware.1-tkg.1.fc82c41    vmwarePhoton64Guest   ovf
ob-17861429-photon-3-k8s-v1.20.2---vmware.1-tkg.1.1d4f79a    v1.20.2+vmware.1-tkg.1.1d4f79a    vmwarePhoton64Guest   ovf
```

# Creating Clusters



# Upgrading Clusters

## Upgrading Tanzu Kubernetes Clusters (TKC)

You cannot decrease the version. For example, you cannot downgrade from Kubernetes v1.18 to v1.17.

You can upgrade the minor version, but only incrementally. Skipping minor versions is not supported. For example, you can upgrade from Kubernetes v1.17 to v1.18, but you cannot upgrade from Kubernetes v1.16 to v1.18.

You cannot change the major version, such as upgrading from v1.18 to v2.0.

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-DF2B3886-4BE0-4E88-B549-DC9C1C653FDB.html

Version updates of kubernetes can be updated by 1 or 2 methods:

Edit the manifest directly: 
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-A7A0BC51-F49D-4E08-B4DD-782D84CB3762.html


Use Patch Method:
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-C285CC64-93F6-4EB3-9CBA-7903DE74755E.html


You can also edit and re-apply the yalm file that was originally used, but this is NOT recommended. (see: https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-DF2B3886-4BE0-4E88-B549-DC9C1C653FDB.html#cluster-manifest-update-methods-2)



### Example

Creating example cluster at lower k8s version:

Log into the supervisor cluster context



```
 kubectl apply -f example-small-.yaml

tanzukubernetescluster.run.tanzu.vmware.com/tkgs-example-cluster-small created

kubectl get tkc -A
NAMESPACE         NAME                         CONTROL PLANE   WORKER   DISTRIBUTION                      AGE     PHASE      TKR COMPATIBLE   UPDATES AVAILABLE
example-namespace-ns-01   tkgs-c04-large               3               3        v1.18.15+vmware.1-tkg.1.600e412   4d18h   running    True             [1.19.7+vmware.1-tkg.1.fc82c41]
example-namespace-ns-01   tkgs-cl01                    3               3        v1.19.7+vmware.1-tkg.1.fc82c41    5d19h   running    True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-cl03-large              3               3        v1.19.7+vmware.1-tkg.1.fc82c41    5d13h   running    True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-example-cluster-small   1               1        v1.18.15+vmware.1-tkg.1.600e412   11s     creating   True             [1.19.7+vmware.1-tkg.1.fc82c41]
example-namespace-ns-01   tkgs-infra-services          3               4        v1.18.15+vmware.1-tkg.1.600e412   4d      running    True             [1.19.7+vmware.1-tkg.1.fc82c41]

```

Wait till our example cluster has been created. It will have K8s version 1.18.15

Retrieve the available kubernetes versions we can update to:

```
kubectl get tanzukubernetesreleases
NAME                                VERSION                          READY   COMPATIBLE   CREATED   UPDATES AVAILABLE
v1.16.12---vmware.1-tkg.1.da7afe7   1.16.12+vmware.1-tkg.1.da7afe7   True    True         7d10h     [1.17.17+vmware.1-tkg.1.d44d45a 1.16.14+vmware.1-tkg.1.ada4837]
v1.16.14---vmware.1-tkg.1.ada4837   1.16.14+vmware.1-tkg.1.ada4837   True    True         7d10h     [1.17.17+vmware.1-tkg.1.d44d45a]
v1.16.8---vmware.1-tkg.3.60d2ffd    1.16.8+vmware.1-tkg.3.60d2ffd    False   False        7d10h     [1.17.17+vmware.1-tkg.1.d44d45a 1.16.14+vmware.1-tkg.1.ada4837]
v1.17.11---vmware.1-tkg.1.15f1e18   1.17.11+vmware.1-tkg.1.15f1e18   True    True         7d10h     [1.18.15+vmware.1-tkg.1.600e412 1.17.17+vmware.1-tkg.1.d44d45a]
v1.17.11---vmware.1-tkg.2.ad3d374   1.17.11+vmware.1-tkg.2.ad3d374   True    True         7d10h     [1.18.15+vmware.1-tkg.1.600e412 1.17.17+vmware.1-tkg.1.d44d45a]
v1.17.13---vmware.1-tkg.2.2c133ed   1.17.13+vmware.1-tkg.2.2c133ed   True    True         7d10h     [1.18.15+vmware.1-tkg.1.600e412 1.17.17+vmware.1-tkg.1.d44d45a]
v1.17.17---vmware.1-tkg.1.d44d45a   1.17.17+vmware.1-tkg.1.d44d45a   True    True         7d10h     [1.18.15+vmware.1-tkg.1.600e412]
v1.17.7---vmware.1-tkg.1.154236c    1.17.7+vmware.1-tkg.1.154236c    True    True         7d10h     [1.18.15+vmware.1-tkg.1.600e412 1.17.17+vmware.1-tkg.1.d44d45a]
v1.17.8---vmware.1-tkg.1.5417466    1.17.8+vmware.1-tkg.1.5417466    True    True         7d10h     [1.18.15+vmware.1-tkg.1.600e412 1.17.17+vmware.1-tkg.1.d44d45a]
v1.18.10---vmware.1-tkg.1.3a6cd48   1.18.10+vmware.1-tkg.1.3a6cd48   True    True         7d10h     [1.19.7+vmware.1-tkg.1.fc82c41 1.18.15+vmware.1-tkg.1.600e412]
v1.18.15---vmware.1-tkg.1.600e412   1.18.15+vmware.1-tkg.1.600e412   True    True         7d10h     [1.19.7+vmware.1-tkg.1.fc82c41]
v1.18.5---vmware.1-tkg.1.c40d30d    1.18.5+vmware.1-tkg.1.c40d30d    True    True         7d10h     [1.19.7+vmware.1-tkg.1.fc82c41 1.18.15+vmware.1-tkg.1.600e412]
v1.19.7---vmware.1-tkg.1.fc82c41    1.19.7+vmware.1-tkg.1.fc82c41    True    True         7d10h     [1.20.2+vmware.1-tkg.1.1d4f79a]
v1.20.2---vmware.1-tkg.1.1d4f79a    1.20.2+vmware.1-tkg.1.1d4f79a    True    True         7d10h
```


we will update our example cluster from 1.18.5 to 1.19.7

```
 read -r -d '' PATCH <<'EOF'
                                                                                 spec:
                                                                                   distribution:
                                                                                     fullVersion: null    # NOTE: Must set to null when updating just the version field
                                                                                     version: v1.19.7
                                                                                 EOF
echo $PATCH
spec: distribution: fullVersion: null # NOTE: Must set to null when updating just the version field version: v1.19.7
```

```
kubectl patch --type=merge tanzukubernetescluster TKG-CLUSTER-NAME --patch "$PATCH"

kubectl patch --type=merge tanzukubernetescluster tkgs-example-cluster-small --patch "$PATCH" --namespace example-namespace-ns-01
tanzukubernetescluster.run.tanzu.vmware.com/tkgs-example-cluster-small patched
```

our example cluster should now have status of updating:

```
kubectl get tkc -A
NAMESPACE         NAME                         CONTROL PLANE   WORKER   DISTRIBUTION                      AGE     PHASE      TKR COMPATIBLE   UPDATES AVAILABLE
example-namespace-ns-01   tkgs-c04-large               3               3        v1.18.15+vmware.1-tkg.1.600e412   4d19h   running    True             [1.19.7+vmware.1-tkg.1.fc82c41]
example-namespace-ns-01   tkgs-cl01                    3               3        v1.19.7+vmware.1-tkg.1.fc82c41    5d20h   running    True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-cl03-large              3               3        v1.19.7+vmware.1-tkg.1.fc82c41    5d13h   running    True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-example-cluster-small   1               1        v1.19.7+vmware.1-tkg.1.fc82c41    31m     updating   True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-infra-services          3               4        v1.18.15+vmware.1-tkg.1.600e412   4d1h    running    True             [1.19.7+vmware.1-tkg.1.fc82c41]
```


## Define and edit Node / Cluster Sizes
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-7351EEFF-4EF0-468F-A19B-6CEA40983D3D.html





## Assign role binding / Grant access to developers/users 


https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-6DE4016E-D51C-4E9B-9F8B-F6577A18F296.html

Developers are the target users of Kubernetes. Once a Tanzu Kubernetes cluster is provisioned, you can grant developer access using vCenter Single Sign-On authentication.

Developers must have access to the kubectl and kubectl-vsphere command line tools, that they can download from the supervisor cluster homepage


Example role bindings can be found here: TKGs-RoleBindingExamples\RoleBindingExample_vSphereSSO_Local_User.yml

The Tanzu Kubernetes Grid Service does not provide default RoleBinding and ClusterRoleBinding
for Tanzu Kubernetes clusters.

A vCenter Single Sign-On user who is granted the Edit permission on a Supervisor Namespace is
assigned to the cluster-admin role for any Tanzu Kubernetes cluster deployed in that
namespace. An authenticated cluster administrator can implicitly use the vmware-systemprivileged PSP. Although not technically a ClusterRoleBinding, it has the same effect.

The cluster administrator must define any bindings to allow or restrict the types of pods users
can deploy to a cluster. If a RoleBinding is used, the binding only allows pods to be run in the
same namespace as the binding. This can be paired with system groups to grant access to all
pods run in the namespace. Non-admin users who authenticate to the cluster are assigned to the
authenticated role and can be bound to default PSP as such.


### privileged workloads

https://kubernetes.io/docs/concepts/policy/pod-security-policy/#privileged
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-4CCDBB85-2770-4FB8-BF0E-5146B45C9543.html


Privileged
Privileged - determines if any container in a pod can enable privileged mode. By default a container is not allowed to access any devices on the host, but a "privileged" container is given access to all devices on the host. This allows the container nearly all the same access as processes running on the host. 
This is useful for containers that want to use linux capabilities like manipulating the network stack and accessing devices


The following kubectl command creates a ClusterRoleBinding that grants access to authenticated users run a privileged set of workloads using the default PSP vmware-system-privileged.

```
kubectl create clusterrolebinding default-tkg-admin-privileged-binding --clusterrole=psp:vmware-system-privileged --group=system:authenticated
```


# Storage Tasks

## Storage Management Tasks of a vSphere Administrator
Generally, the persistent storage management tasks in vSphere with Tanzu include the following. As a vSphere administrator, you use the vSphere Client to perform these tasks.

Perform lifecycle operations for the VM storage policies

Provide storage resources to the DevOps engineers by assigning the storage policies to the namespace and by setting storage limits

Monitor Kubernetes objects and their storage policy compliance in the vSphere Client.



## Storage Management Tasks of a DevOps Engineer

Typically, the DevOps engineer uses kubectl to perform the following storage tasks.

## Manage storage classes. 

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-5C367B26-59B0-448F-B071-07885A47FFFB.html

In the supervisor cluster context:
kubectl get storageclass
```
[ kubectl get storageclass
NAME    PROVISIONER              RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
tanzu   csi.vsphere.vmware.com   Delete          Immediate           true                   2d3h
```
Note:
This command is available only to a user with administrator privileges.

```
[ kubectl describe namespace example-namespace-ns-01
Name:         example-namespace-ns-01
Labels:       vSphereClusterID=domain-c1006
Annotations:  vmware-system-resource-pool: resgroup-2059
              vmware-system-vm-folder: group-v2060
Status:       Active

Resource Quotas
 Name:                                               example-namespace-ns-01-storagequota
 Resource                                            Used  Hard
 --------                                            ---   ---
 tanzu.storageclass.storage.k8s.io/requests.storage  0     9223372036854775807

No LimitRange resource.
```

In the namespace context:

```
[ kubectl describe resourcequotas
Name:                                               example-namespace-ns-01-storagequota
Namespace:                                          example-namespace-ns-01
Resource                                            Used  Hard
--------                                            ----  ----
tanzu.storageclass.storage.k8s.io/requests.storage  0     9223372036854775807
```

## Set the default storage class

vSphere with Tanzu does not set a default storage class for Tanzu Kubernetes Clusters it creates. 

So if you want PersistantVolumeClaims to always use a specific Storage Policy, without developers having to explicitely set it each time, you must set this yourself. 


You do this by adding athe 'defaultClass' to the Spec in the yaml for the cluster definition




### Changing the default storage class for existing clusters

https://anthonyspiteri.net/tanzu-no-default-storageclass/


You must edit this, per cluster, from the supervisorcluster


```[
   kubectl get tkc -A
NAMESPACE         NAME              CONTROL PLANE   WORKER   DISTRIBUTION                     AGE   PHASE     TKR COMPATIBLE   UPDATES AVAILABLE
example-namespace-ns-01   tkgs-cl01         3               3        v1.19.7+vmware.1-tkg.1.fc82c41   24h   running   True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-cl02         3               3        v1.19.7+vmware.1-tkg.1.fc82c41   24h   running   True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-cl03-large   3               3        v1.19.7+vmware.1-tkg.1.fc82c41   17h   running   True             [1.20.2+vmware.1-tkg.1.1d4f79a]
example-namespace-ns-01   tkgs-cl19         1               1        v1.19.7+vmware.1-tkg.1.fc82c41   22h   running   True             [1.20.2+vmware.1-tkg.1.1d4f79a]
[
[
[ kubectl edit tkc tkgs-cl01 --namespace example-namespace-ns-01
```







## Define and deploy persistant volume claims that match the storage class (that in turn; match the vSphere storage policy)

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-8449965C-0E27-4783-B762-FEFB96BD5F98.html
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-1B136277-E46C-41FC-9C8C-3E78E9B97F5C.html


To run stateful workloads on Tanzu Kubernetes clusters, you can create a persistent volume
claim (PVC) to request persistent storage resources without knowing the details of the
underlying storage infrastructure. The storage used for the PVC is allocated out of the storage
quota for the vSphere Namespace.


Containers by default are ephemeral and stateless. For stateful workloads, a common approach
is to create a persistent volume claim (PVC). You can use a PVC to mount the persistent volumes
and access storage. The request dynamically provisions a persistent volume object and a
matching virtual disk. The claim is bound to the persistent volume. When you delete the claim, 

Examples can be found for the Guestbook application: 

app-examples\Guestbook\redis-leader-pvc.yaml
app-examples\Guestbook\redis-follower-pvc.yaml



## Deploy and manage stateful applications. 


This could be done by the 'DevOps engineer Role' , or by developers, depending on how complicated the deployment is. 
Complex deployment should be done cross-collaboration. 

Example of a stateful application: 
app-examples\Guestbook
app-examples\Guestbook\Guestbook_example_readme.md




## Perform lifecycle operations for persistent volumes. 

### Volume Expansion

https://vsphere-csi-driver.sigs.k8s.io/features/volume_expansion.html
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-8D7C8AAA-BD59-49EF-AB02-C1B2FF46F59B.html

Patch the PVC to increase its requested storage size (in this case, to 2Gi):

```
$ kubectl patch pvc example-block-pvc -p '{"spec": {"resources": {"requests": {"storage": "2Gi"}}}}'
persistentvolumeclaim/example-block-pvc patched
```

# Troubleshooting

Main troubleshooting docs:

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-75B06734-4D2C-4219-B220-413ADE8808C6.html


## WCPsvc.log on VCSA

The main log files for vSphere with Tanzu is located on the vCenter VCSA, in the location
“/var/log/vmware/wcp/wcpsvc.log”.

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-F7B30D53-A839-40D9-8D8C-2262AAA5BAD0.html




## SSH Into TKC Nodes

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-37DC1DF2-119B-4E9E-8CA6-C194F39DDEDA.html

### Example

Log into the supervisor cluster

Switch context to the namespace entry that has the supervisorcluster IP behind it

```
kubectl config use-context example-namespace-ns-01-10.6.171.128
```

Get the details for the VM you want to SSH into, what you need here is the IP address. 

```
kubectl describe virtualmachine tkgs-infra-services-workers-zfxbr-7c488875f7-2smph
```

```
kubectl get secrets tkgs-infra-services-ssh-password -o yaml
apiVersion: v1
data:
  ssh-passwordkey: ZkExNmhpNjBkdk52MXZrVVhCRU00L0J1dTUySklLbXMya0dpTThETm5kND0=
kind: Secret
metadata:
  creationTimestamp: "2021-05-07T07:52:11Z"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:ssh-passwordkey: {}
      f:metadata:
        f:ownerReferences:
          .: {}
          k:{"uid":"6ef2fa38-54cf-4f8f-b41d-389bee14b09f"}:
            .: {}
            f:apiVersion: {}
            f:kind: {}
            f:name: {}
            f:uid: {}
      f:type: {}
    manager: manager
    operation: Update
    time: "2021-05-07T07:52:11Z"
  name: tkgs-infra-services-ssh-password
  namespace: example-namespace-ns-01
  ownerReferences:
  - apiVersion: run.tanzu.vmware.com/v1alpha1
    kind: TanzuKubernetesCluster
    name: tkgs-infra-services
    uid: 6ef2fa38-54cf-4f8f-b41d-389bee14b09f
  resourceVersion: "2936435"
  selfLink: /api/v1/namespaces/example-namespace-ns-01/secrets/tkgs-infra-services-ssh-password
  uid: 1efb435e-f35e-4ce3-8108-40965bc0c2fb
type: Opaque
```


The SSH Key is base64 coded, to decode:

```
echo ZkExNmhpNjBkdk52MXZrVVhCRU00L0J1dTUySklLbXMya0dpTThETm5kND0= | base64 --decode

fA16hi60dvNv1vkUXBEM4/Buu52JIKms2kGiM8DNnd4=
```

SSH into the node

```
ssh vmware-system-user@10.6.169.131
The authenticity of host '10.6.169.131 (10.6.169.131)' can't be established.
ECDSA key fingerprint is SHA256:DEUojOlEav7BNRtZ/hLkqFuVyG0+fKLL1UI2vZwX1k8.
ECDSA key fingerprint is MD5:b9:96:87:fc:34:a1:53:13:8d:05:45:17:cb:34:9a:45.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.6.169.131' (ECDSA) to the list of known hosts.
Welcome to Photon 3.0 (\m) - Kernel \r (\l)
vmware-system-user@10.6.169.131's password:
 21:41:31 up 2 days, 12 min,  0 users,  load average: 0.03, 0.11, 0.08

33 Security notice(s)
Run 'tdnf updateinfo info' to see the details.
vmware-system-user@tkgs-infra-services-workers-zfxbr-7c488875f7-2smph [ ~ ]$

```


# TKGs Extentions vs TKGm Packages

TKGs 'Extentions' TKG 1.3 and prior) are to be considdered deprecated and not to be used. 

Instead use TKG Packages that use the new Carvel kapp-controller. 

For more information, and for TKGs specific regarding this, see seperate document [TKGs-using-TKG-packages.md](TKGs-using-TKG-packages.md)
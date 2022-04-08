# As a vSphere Administrator you need to enroll your Developers on the vSphere with Tanzu Platform, how?

A vSphere Namespace can be considered, at a base, to have similar behaviour and characteristics as a Resource Pool in vCenter. A vSphere Namespace is a piece of your Data Center that you provie to your Developers for deploying vSphere Pods, Tanzu Kubernetes Clusters (TKC) and Worklaods on those TKCs. 

As a **vSphere Administrator you need to minimally do** the following:

1. **Create** a vSphere Namespace
1. Configure **Storage Policies** for that Namespace to be used by Persistent Volumes and Tanzu Kubernetes Cluster Nodes
1. Configure the **Content Library** containing the OVA files for creating Tanzu Kubernetes Clusters under the Tanzu Kubernetes Grid Service in the vSphere Namespace
1. Configure **VM Classes (Node Sizes)** in the vSphere Namespace under VM Service that define the Sizes of your TKC Nodes
1. Configure **Resource Limits** corresponding to your needs. E.g.: Configure Resource Limits to the largest VM Size (VM Class) x the maximum number of TKC Nodes you allow
1. **Assign the necessary permissions** to your Developers. Ideally at least **'Edit' or 'Owner'** Permissions so that your Devs can create TKCs themselves. Permissions are explained on our github [here](../../TKGs-basic-usage-examples.md#vsphere-namespace-roles). 

**Provide your Devs** with the **following** Details:
* The Supervisor **Control Plane IP Address** & Website Link to download the Kubernetes CLI Tools for vSphere
* The **Storage Policies** used for Persistent Volumes and Tanzu Kubernetes Cluster Nodes
* [Install Kubernetes CLI Tools for vSphere](Content/Install-Kubernetes-CLI-Tools-for-vSphere.md)
* [How to Login?](Content/How-to-Login.md) 
* [How to Create a Tanzu Kubernetes Cluster?](Content/How-to-Create-a-Tanzu-Kubernetes-Cluster.md)
* [How to Delete a Tanzu Kubernetes Cluster?](Content/How-to-Delete-a-Tanzu-Kubernetes-Cluster.md)
* [How to Scale an existing Tanzu Kubernetes Cluster?](Content/How-to-Scale-an-existing-Tanzu-Kubernetes-Cluster.md)
* [How to Upgrade a Tanzu Kubernetes Cluster?](Content/How-to-Upgrade-a-Tanzu-Kubernetes-Cluster.md)
* [How to Apply a Pod Security Policy on a Tanzu Kubernetes Cluster?](Content/How-to-Apply-a-Pod-Security-Policy-on-a-Tanzu-Kubernetes-Cluster.md)
* [How to create an L4 Load Balancer with NSX-ALB (Avi)?](Content/How-to-create-an-L4-Load-Balancer-with-Avi.md) 
* [How to use NSX-ALB (Avi) as an L7 Ingress Controller?](Content/How-to-use-NSX-ALB-(Avi)-as-an-L7-Ingress-Controller.md)
* [How to integrate your own Container Registry?](Content/How-to-integrate-your-own-Container-Registry.md)
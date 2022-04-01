# Current TKG Limitations and Issues

This page is to record and explan currentl issues and limitations with the TKG products, that fall broadly under the following categories

* Obvious product limitations that would surprise/shock the customer and require explanation and/or mitigation in some form (either by us, the customer or VMware)
* Product flaws or constraints that impact the proposed solution design
* Product flaws or constraints that cause errors during normal use or abnormalities that require explanation or guidance
* Flaws or missing items in VMware official product documentation
* Issues with integration with external software (for example NSX-T or NSX ALB)
* Issues with standard VMware Carvel Packages


# TKGs (vSphere with Tanzu)


## Documentation

Core documentation Workload Enablement walkthrough is outdated


## Interaction with vSphere and ESX hosts

DRS Must remain on at all times after workload enablement. (but you can set to manual if needed). Disabling DRS leads to breaking your Tanzu Kubernetes clusters.
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-8D7D292B-43E9-4CB8-9E20-E4039B80BF9B.html


## Networking

After Workload Enablement, additional workload netwoks can only be added if the Management network uses STATIC networking. 
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-1E7B905B-2D3E-4E60-9F37-8C0C3D8F7771.html
For all practical purposes, this means you must always configure management network with static IPs


## vSphere Namespaces



## vSphere Services

The 'vSphere Services' section (the 'services' tab) under Workload Management, seems extremely underdeveloped. The links point to a [Github documentation page](https://github.com/vsphere-tmm/Supervisor-Services), which in turn links directly to [Jfrog artifactory](https://vmwaresaas.jfrog.io/ui/repos/tree/General) for direct download of service defintions (this should be in Partner Connect Downloads or TanzuNet), and the entire thing seems to be maintained by just 1 or 2 guys. What little documentation there is, seems to be written by the same guys: https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-A0A5F6D4-87A4-46CA-A50A-33664F43F299.html#GUID-A0A5F6D4-87A4-46CA-A50A-33664F43F299
That this part shipped in this state is unbelievable. 


## CSI Issues

The CSI seems to create a LB service. (where before it was internal cluster-ip) This is undocumented in either TKGs or CSI docs. 

CSI driver inside TKGs clusters, create a LB entry specifically for the CSI. This is undocumented. The ports it expects on the Master nodes is also not enabled by default, so this VS turns up and remains RED in the Avi overview. 


## NSX ALB (Avi) Integration

### TKGs Limited to 'Default Cloud'
Workload Enablement wizard does not allow selection of any other NSX ALB 'cloud'. It can only use the 'default' cloud in Avi.  This is a serious limitation in any case that more than 1 'cloud' defintition are required on the AVI side. For example seperate SE groups using seperate networks. 
TO CHECK: Can TKGs cluster defintions override 'default' cloud for the AKO config? 

### TKGs Relies on 'Basic Authentication'
At some point in 2021, NSX ALB stopped supported Basic Auth as a method to authenticate against the API. However TKGs (as of feb 2022) still requires this.
Workaround procedure is documented here: https://vraccoon.com/2021/10/the-control-plane-vm-was-unable-to-authenticate-to-the-load-balancer-avi/![image](https://user-images.githubusercontent.com/29251599/152826504-378a52ab-e122-4e95-bf35-d50a9889e133.png)
The procedure to set this is not documented anywhere in VMware docs. 

Procedure:
* SSH into NSX ALB Controller
* Start the shell `shell --user admin`
* Move to systemconfiguration `configure systemconfiguration`
* Move to portalconfiguration `portal_configuration`
* Note Line 27
* Change value by typing `allow_basic_authentication`
* Exit Out

# TKGm (Tanzu Kubernetes Grid)


## Dex

Dex self-signed certificates expire by default after 90 days. 
This is not documented. 
If certificates are not rolled over within 90 days, Pinniped will not be able to run LDAP autnentication against Dex, and Logins will fail. 
This results in an error (in the browser) 'no upstream providers configured'

Workarounds: 

Set up Dex to use custom cert that has a longer lifespan. 
Or just have Dex roleover its cert (so its valid for another 90 days. Both procedures are documented here: 
https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.5/vmware-tanzu-kubernetes-grid-15/GUID-iam-custom-pinniped-certificates.html#update-dex-serving-certificate-and-dex-ca-certificate-3

```
Issue: No upstream Providers Configured error was thrown in the Browser when customer tried to login to LDAP
Workaround/Resolution Commands 
- kubectl config use-context management-context
- kubectl get certificate dex-cert -n tanzu-system-auth
- kubectl edit certificate dex-cert -n tanzu-system-auth
under dex:
certificate:
duration: 87600h0m0s. //modify the duration for 10 years or less than that as per requirement

- kubectl get secrets -n tanzu-system-auth
- kubectl delete secret dex-cert-tls -n tanzu-system-auth
- kubectl rollout restart deployment/dex -n tanzu-system-auth

```

## Pinniped

### Spontanious Failure to update workload cluster secrets with new certificate information

Cause currently not well understood. VMware plans to fix in future TKGm version. 

This is documented here: 
https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.5/vmware-tanzu-kubernetes-grid-15/GUID-troubleshooting-tkg-tips.html#pinniped-authentication-error-on-workload-cluster-after-management-cluster-upgrade-17
The workaround is also documented on the same page: https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.5/vmware-tanzu-kubernetes-grid-15/GUID-troubleshooting-tkg-tips.html#postdeploy-pinniped-job-fails-16

Example of workaround procedure: 

```
Issue2:  could not complete concierge credential exchange: login failed While trying to run commands from console
Resolution Commands 
1- To check certificates being used by pinniped-supervisor service 
- openssl s_client -showcerts -servername 172.2.50.21 -connect 172.2.50.21:31234
2- Capture/Copy CA Certificate being used and paste output in a file named ca-openssl.cert
vim ca-openssl.cert
cat ca-openssl.cert | base64 -w0 ---> To encrypt CA certificate 
3- Switch context to management cluster 
kubectl config use-context <management-cluster-name>-admin@<management-cluster-name>
4- Get pinniped-addon secret for workload cluster 
kubectl get secret <workload-cluster-name>-pinniped-addon -oyaml
5- Decrypt encrypted file values.yaml 
echo "<Values.yaml>" | base64 -d > values.yaml
6- Edit in decrypted file values.yaml in part pinniped-supervisor_ca_bundle_data and paste encypted value of ca-openssl.cert file 
vim values.yaml ---> Save after pasting the value of encrypted CA bundle data 
7- Encrypt the file values.yaml back again to add the corrected value in workload pinniped-addon secret 
cat values.yaml | base64 -w0
8- Edit in the pinniped-addon secret for workload cluster 
kubectl edit secret <workload-cluster-name>-pinniped-addon 
9- Switch back to workload cluster to delete post-deploy-job in pinniped-supervisor namespace 
kubectl config use-context <workload-cluster-name>-admin@<workload-cluster-name>
10- Delete the post-deploy-job 
kubectl get jobs -A
kubectl delete job  pinniped-post-deploy-job -n pinniped-supervisor 
11- Ensure that correct ca bundle is being used in newly deployed post-deployed-job that was automatically created after deletion 
kubectl get job pinniped-post-deploy-job -n pinniped-supervisor -oyaml
12- Try to test login again and it will work successfully 
```


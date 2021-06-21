# Naming Guidelines


## Server Names

Many of our examples will be scripts or commands run from a jumpbox. 

Refer to these by the following names:

- linuxjumpbox
- windowsjumpbox


Keep servernames or service names as generic as possible

Examples

harborpoc (harborpoc.customerdomain.local)





## IP Addresses

IP addresses are to be taken from a fictional 10.0.0.0/24 range

Example: 10.0.1.20


## Active Directory or DNS Domains

All examples of a customers active directory domain, should be 'customerdomain.local'

The name of the customer comapany name is to be referred to as 'customer' 

For examples that concern vSphere or vRA SSO, the default SSO domain can be used: 'vsphere.local' 



## Usernames

No examples of any real-world usernames are to be used! 

Usernames are to be referred to in terms of 'personas' only. 

Examples:

developer1@customerdomain.local

ITadmin1@customerdomain.local

DevOps1@customerdomain.local


## Kubernetes objects

Keep names generic, but make it clear from the name, what the resourcetype is. 

### Storage

storageclasses examples:

- storageclass-tanzu-nodestorage
- storageclass-tanzu-persistantvolumes


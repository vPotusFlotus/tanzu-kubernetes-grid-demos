# Naming Guidelines


## Customer Information

Under no circumstances should customer specific data end up in this repo. Any URLs, server names, IP addresses, etc should be made generic before that code is added to this repo. 


## Passwords, Secrets, Certificates and other Key Material

Under no circumstances should customer secrets end up in this repo. 
 If you develop a demonistration during a customer engagement, and copy paste it for use here, scrub any and all secrets from the example or replace it with placeholders that are clearly commented as such


## Home-lab specific information

If you are adding examples of anything that contains URLS, IP's, ranges, secrets etc from your own home-lab, you do so under your own responsibilty. Otherwise make the example generic according to the guidelines at the bottom section of this document. 

Make sure you mark such values or explain where these values come from in comments, or an added Readme.md file


## Use folders and Make Readme.md files to explain what you have added

Its just common curtesy to explain your example or demo
If you add a folder with many config file examples, explain generally what its for and where its to be used. 


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


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

CSI LB service is undocumented in either TKGs or CSI docs. 


## NSX ALB (Avi) Integration

Workload Enablement wizard does not allow selection of any other NSX ALB 'cloud'. It can only use the 'default' cloud in Avi.  This is a serious limitation in any case that more than 1 'cloud' defintition are required on the AVI side. For example seperate SE groups using seperate networks. 
TO CHECK: Can TKGs cluster defintions override 'default' cloud for the AKO config? 

CSI driver inside TKGs clusters, create a LB entry specifically for the CSI. This is undocumented. The ports it exects on the Master nodes is also not enabled by default, so this VS turns up and remains RED in the Avi overview. 


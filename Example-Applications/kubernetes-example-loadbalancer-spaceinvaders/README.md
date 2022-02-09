# Load Balancer Example

This Example will deploy the SpaceInvaders Application on an HTML 5 Website based on NGINX. 
The Application will be exposed via a service of type LoadBalancer. 

## 1. Modify the nginx-spaceinvaders.yaml to your likings
```
vi nginx-spaceinvaders.yaml
```
## 2. Deploy the SpaceInvaders application!
```
kubectl apply -f nginx-spaceinvaders.yaml
```
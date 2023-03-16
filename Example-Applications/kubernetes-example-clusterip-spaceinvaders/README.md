# ClusterIP Service Example

This Example will deploy the SpaceInvaders Application on an HTML 5 Website based on NGINX. 
The Application will be exposed internally a service of type ClusterIP. 
Later you can expose this ClusterIP Service with an Ingress for example.  

## 1. Modify the nginx-spaceinvaders.yaml to your likings
```
vi nginx-spaceinvaders.yaml
```
## 2. Deploy the SpaceInvaders application!
```
kubectl apply -f nginx-spaceinvaders.yaml
```
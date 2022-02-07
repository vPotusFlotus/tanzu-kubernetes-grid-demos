# Ingress Controller Example

This example application will deploy '3 Versions' of a BusyBox Website. Each Version has its own Deployment.
Each Version's Deployment is exposed internally via a ClusterIP Service.

This ClusterIP Service can be exposed externally via an Ingress Controller. 

The Websites can eventually be accessed by browsing to the Ingress Controller's Hostname you've set and appending '/v1' or '/v2' or '/v3' to them. 

Special thanks to Nicolas Bayle who provided his [Github Repository](https://github.com/tacobayle) during the NSX-ALB Architecture Course. 

## 1 . Deploy 3 Versions of the Busybox Web App

```
kubectl apply -f https://raw.githubusercontent.com/tacobayle/k8sYaml/master/k8sDeploymentBusyBoxFrontEndV1.yml
kubectl apply -f https://raw.githubusercontent.com/tacobayle/k8sYaml/master/k8sDeploymentBusyBoxFrontEndV2.yml
kubectl apply -f https://raw.githubusercontent.com/tacobayle/k8sYaml/master/k8sDeploymentBusyBoxFrontEndV3.yml
```

## 2. Create for each Version a Service Type ClusterIP
```
kubectl apply -f https://raw.githubusercontent.com/tacobayle/k8sYaml/master/k8sSvcClusterIpBusyBoxFrontEndV1.yml
kubectl apply -f https://raw.githubusercontent.com/tacobayle/k8sYaml/master/k8sSvcClusterIpBusyBoxFrontEndV2.yml
kubectl apply -f https://raw.githubusercontent.com/tacobayle/k8sYaml/master/k8sSvcClusterIpBusyBoxFrontEndV3.yml
```

## 3. Modify the ingress.yaml file to represent your hostname
```
vi ingress.yaml
```

## 4. Deploy the Ingress Controller
kubectl apply -f ingress.yaml

## 5. Browse to the websites!

- http://yourIngressHostname/v1
- http://yourIngressHostname/v2
- http://yourIngressHostname/v3
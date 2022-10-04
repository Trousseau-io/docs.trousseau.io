
## HashiCorp Vault
Here are the requirements based on the environment type: 

| Environment | k8s version | Control Plane | Vault version    | Vault in k8s | Vault external | Vault Cloud |
|-------------|-------------|---------------|------------------|--------------|----------------|-------------|
| dev/test    | 1.18+       | 1 node        | 1.9+ Community   | yes          | yes            | yes         |
| production  | 1.22+       | 3 nodes       | 1.10+ Enterprise | no           | yes            | yes         |


## Pre-deployment Secret

Create a secret to verify Trousseau's deployment
```
kubectl create secret generic secret-pre-deploy -n default --from-literal=mykey=mydata
```

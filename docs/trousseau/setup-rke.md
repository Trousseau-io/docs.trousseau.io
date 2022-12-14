
## Enable the KMS Provider Plgin 

Create the KMS provider plugin file
```YAML title="trousseau-vault-encryptionconfiguration.yml"
--8<-- "trousseau/files/trousseau-vault-encryptionconfiguration.yml"
```

## RKE 1 
TBA 

## RKE 2

!!! warning "RKE2 Encryption at Rest"
    By default, a RKE2 installation includes the encryption at rest of secrets using a ```aesgcm``` or ```aescbc``` (see [Kubernetes Secrets Management](https://docs.trousseau.io/trousseau/secretmanagement/) for more details).
    
    If you plan to enable Trousseau on an existing RKE2 deployment please consider the specific section. 
    
Create a RKE configuration file ```/etc/rancher/rke2/config.yaml``` 

```YAML title="trousseau-rke2-config.yml"
--8<-- "trousseau/files/trousseau-rke2-config.yml"
```
* Creating the Trousseau DaemonSet file ```trousseau-vault-daemonset.yml``` in the ```/var/lib/rancher/rke2/server/manifests/``` folder for RKE2 to pick it up automatically.   
* Create the ```trousseau-vault-encryptionconfiguration.yml``` in the ```/var/lib/rancher/rke2/server/cred/``` folder.
* Verify the Trousseau's deployment status
```
kubectl get pod -n kube-system
```
* Restart your API server only of Trousseau is up and running for at least 60 seconds without any restart.
```
systemctl restart rke2-server
```

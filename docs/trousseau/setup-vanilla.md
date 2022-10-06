
## Enable the KMS Provider Plgin 

Create the KMS provider plugin file
```YAML title="trousseau-vault-encryptionconfiguration.yml"
--8<-- "trousseau/files/trousseau-vault-encryptionconfiguration.yml"
```

## Vanilla Kubernetes 
Set the `--encryption-provider-config` flag for the [kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) to point to the location of the configuration file.


Apply Trousseau's DaemonSet
```
kubectl apply -f trousseau-vault-daemonset.yml
```
```YAML title="trousseau-vault-daemonset.yml"
--8<-- "trousseau/files/trousseau-vault-daemonset.yml"
```

Verify the Trousseau's deployment status
```
kubectl get pod -n kube-system
```

Restart your API server only of Trousseau is up and running for at least 60 seconds without any restart.

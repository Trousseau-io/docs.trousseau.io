# Kubernetes Secrets Management

## Treat Modeling 

                                          
| Methode                                    | Disk | RAM | etcd | API Server | KMS Server |
|--------------------------------------------|------|-----|------|------------|------------| 
| Native                                     | :material-close: | :material-close: | :material-close: | :material-close: | :material-close: |
| File System Encryption                     | :material-check: | :material-close: | :material-close: | :material-close: | :material-close: |
| [External Secrets Operator](https://external-secrets.io/) | :material-close: | :material-close: | :material-close: | :material-close: | :material-check: [1] |
| [SOPS](https://github.com/mozilla/sops)    | :material-close: | :material-close: | :material-close: | :material-close: | :material-check: [2] |
| KMS Provider Encryption at rest            | :material-close: | :material-close: | :material-check: | :material-close: | :material-close: |
| KMS Provider with Plugin for external KMS  | :material-check: | :material-check: [3] | :material-check: | :heavy_check_mark: [4] | :material-check: [5] |

## Native Kubernetes Default Behavior

Kubernetes is using a distributed key-value data store to record all API Objects definition along with their state and version. To ensure proper processing, data is encoded in base64 removing challenges with special characters.  

Same concept applies to Secret, especially leveraging special characters for extra security, for example:

``` title="secret.yml"
--8<-- "trousseau/files/secret.yml"
```

The above base64 encoded values are ```admin``` and ```p@ssw0rd$```. When creating the Secret with ```kubectl apply -f mysecret.yml```, the following flow will be triggered: 

```mermaid
sequenceDiagram
participant User or App
participant API Server
participant etcd
autonumber
  User or App->>API Server: create Secret
  Note right of User or App: base64 encoded sensitive data
  API Server->>etcd: store Secret
```

When reading the Secret with ```kubectl get -n myapp secrets/mysecret -o yaml```, the following flow will be triggered:  

```mermaid
sequenceDiagram
participant User or App
participant API Server
participant etcd
autonumber
  User or App->>API Server: request Secret
  API Server->>etcd: recover Secret
  API Server->>User or App: return Secret
  Note left of API Server: base64 encoded sensitive data
```

### Threat assessment 
Zero protection leading to full exposure of Secrets from file system, etcd data store, and Kubernetes API.


## 


## Kubernetes KMS Provider with external KMS

The Kubernetes API Server can encrypt the sensitve data from Secrets using the KMS Provider. In this scenario, an external KMS is used to encrypt in-flight the Data Encryption Key using to encrypt the sensitive data. This process is called an encryption envelop scheme.   

When creating the Secret with ```kubectl apply -f mysecret.yml```, the following flow will be triggered: 

```mermaid
sequenceDiagram
participant User or App
participant etcd
participant API Server
participant KMS Provider
participant KMS Plugin
participant KMS Server
autonumber
  User or App->>API Server: create Secret
  API Server->>KMS Provider: request to encrypt Secret
  Note left of KMS Provider: generate a DEK
  Note left of KMS Provider: encrypt Secret
  KMS Provider->>KMS Plugin: hand off the DEK for encryption
  KMS Plugin->>KMS Server: encrypt DEK with KEK from KMS
  KMS Plugin->>KMS Provider: return encrypted DEK with KID
  KMS Provider->>API Server: return encrypted DEK with KID
  API Server->>etcd: store Secrets and DEK both encrypted
```

### Threat assessment 
This approach mitigates:

- attacker accessing the file system as root
- attacker accessing the etcd data store (etcd being compromised)
- attacker accessing the API Server still requires the KEK hosted on the remote KMS 
- offline copy of etcd data store requires KEK for decryption of DEKs to decrypt Secrets

This approach does not mitigate:

- attacker accessing the API Server memory allocation can read DEKs recently cached

## Trousseau Deployment Workflow 

### Kubernetes & Vault Configuration
```mermaid
sequenceDiagram
participant k8s
participant vault
autonumber
  k8s->>vault: ServiceAccount for Kubernetes Auth
  k8s->>k8s: RBAC rules
  Note right of vault: Enable Transit Engine
  Note right of vault: Create Transit Key
  Note right of vault: Create Transit policy
  Note right of vault: Create Token linked to policy
  Note right of vault: Create Key-Value storing config
  vault->>k8s: Bind vault config to Trousseau Specs
```

### Trousseau Deployment 
```mermaid
sequenceDiagram
participant k8s
participant trousseau
participant vault
autonumber
  Note over k8s,trousseau: Deployment
  k8s->>trousseau: Apply Trousseau DaemonSet
  Note right of trousseau: Vault Agent Sidecar
  trousseau->>vault: Recover Trousseau ConfigMap config
  vault->>trousseau: Inject Transit Engine Key config
  loop Healthcheck
      trousseau->>vault: check Vault connection
      vault->>trousseau: report Vault connection
  end
```

### Trousseau Operations
```mermaid
sequenceDiagram
participant k8s
participant trousseau
participant vault
autonumber
  Note over k8s,vault: Encryption Operation
  k8s->>trousseau: kube-manager sent Secret for Encryption
  trousseau->>vault: Encrypt Secret payload with Transit Key
  trousseau->>k8s: Send encrypted payload to kube-manager
  Note over k8s,vault: Decryption Operation
  k8s->>trousseau: kube-manager sent Secret for Decryption
  trousseau->>vault: Decrypt Secret payload with Transit Key
  trousseau->>k8s: Send decrypted payload to kube-manager
```

### Vault Token Renewal
```mermaid
sequenceDiagram
participant k8s
participant trousseau
participant vault
autonumber
  Note over k8s,vault: Token Rotation
  vault->>vault: Renew Token
  Note right of vault: Update Key-Value config
  k8s->>trousseau: Delete all Trousseau POD
  Note right of trousseau: Vault Agent Sidecar
  trousseau->>vault: Recover Trousseau ConfigMap config
  vault->>trousseau: Inject Transit Engine Key config
  loop Healthcheck
      trousseau->>vault: validate Vault connection
  end
```

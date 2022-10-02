
## Kubernetes KMS Provider Plugin Overview

<!-- ![trousseau diagram](/images/trousseau-diagram.png) -->

```mermaid
graph LR
  A(user/app) --0--> B[Secret];
  B --1--> C(API Server);
  C --2--> L(KMS Provider);
  L --3--> D(KMS Plugin);
  D --4--> E(KMS Server);
  D --5--> L;
  L --6--> C;
  C --7--> F(etcd);

  subgraph Kubernetes
    C
    D
    F
    L
  end
```

0. An User or Application creates a Secret (a ConfigMap could also benefit)
2. The Kubernetes API Server will request the KMS Provider to encrypt the Secret.  
   The KMS Provider generates a DEK (Data Encryption Key) to encrypt the data field.
3. The KMS Provider hands off the DEK to the KMS Plugin for encryption.
4. The KMS Plugin leverage a KMS Server to encryp tthe DEK with a KEK (Key Encryption Key).
5. The KMS Plugin returns to the KMS Provider the encrypted DEK with a KID (Key ID).
6. The KMS Provider returns the encrypted DEK & KID to the API Server.
7. The API Server stores the encrypted as a Secret the encrypted data field, encrypted DEK and KID.


## Workflow 

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

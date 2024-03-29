## Trousseau: KMS Provider with Plugin for external KMS

### Overview 

The Kubernetes API Server can encrypt the sensitve data from Secrets using the KMS Provider. In this scenario, an external KMS is used to encrypt in-flight the Data Encryption Key using to encrypt the sensitive data. This process is called an encryption envelop scheme. 

The Trousseau project is providing an implementation of the Plugin component allowing the interaction of the API Server KMS Provider with an external KMS Server. The current list of supported KMS Servers is available within the [release notes](/trousseau/releasenotes.md).

When creating the Secret with ```kubectl apply -f mysecret.yml```, the following flow will be triggered: 

```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
participant kube-controller-manager
participant kube-scheduler
end
box Compute Node
participant kubelet
participant container runtime
participant Pod
end
autonumber
  User or App->>kube-apiserver: create Pod
  kube-apiserver->>etcd: store Pod specs
  kube-apiserver->>kube-controller-manager: reconcile desired state
  kube-controller-manager->>kube-apiserver: current state different than desired
  kube-apiserver->>kube-scheduler: create Pod
  kube-scheduler->>kube-apiserver: available node
  kube-apiserver->>etcd: store node specs
  kube-apiserver->>kubelet: bind Pod to node
  kubelet->>container runtime: run Pod
  container runtime->>Pod: status
  container runtime->>kubelet: OK
  kubelet->>kube-apiserver: Pod status
  kube-apiserver->>etcd: store Pod status
  kube-apiserver->>User or App: Pod created
```

```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
end
autonumber
  User or App->>kube-apiserver: create Secret
  Note right of User or App: base64 encoded data
  kube-apiserver->>etcd: store Secret
  kube-apiserver->>User or App: Secret created
```

```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
participant kube-controller-manager
participant kube-scheduler
end
box Compute Node
participant kubelet
participant container runtime
participant Pod
end
autonumber
  User or App->>kube-apiserver: create Pod
  kube-apiserver->>etcd: store Pod specs
  kube-apiserver->>kube-controller-manager: reconcile desired state
  kube-controller-manager->>kube-apiserver: current state different than desired
  kube-apiserver->>kube-scheduler: create Pod
  kube-scheduler->>kube-apiserver: available node
  kube-apiserver->>etcd: store Node specs
  kube-apiserver->>kubelet: bind Pod to node
  kubelet->>container runtime: run Pod
  kubelet->>kube-apiserver: get Secret
  kube-apiserver->>kubelet: put Secret
  container runtime->>kubelet: OK
  kubelet->>kube-apiserver: Pod status
  kube-apiserver->>etcd: store Pod status
  kube-apiserver->>User or App: Pod created
```

```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
participant EncryptionConfiguration
end
autonumber
  User or App->>kube-apiserver: create Secret
  kube-apiserver->>EncryptionConfiguration: which provider?
  EncryptionConfiguration->>kube-apiserver: identity
  kube-apiserver->>etcd: store base64-encoded Secret
  kube-apiserver->>User or App: Secret created
```

```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
participant EncryptionConfiguration
end
autonumber
  User or App->>kube-apiserver: create Secret
  kube-apiserver->>EncryptionConfiguration: which provider?
  EncryptionConfiguration->>kube-apiserver: aesgcm
  kube-apiserver->>kube-apiserver: encrypt Secret with key
  kube-apiserver->>etcd: store encrypted Secret
  kube-apiserver->>User or App: Secret created
```

```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
participant EncryptionConfiguration
participant KMS Plugin
end
participant KMS Server
autonumber
  User or App->>kube-apiserver: create Secret
  kube-apiserver->>EncryptionConfiguration: which provider?
  EncryptionConfiguration->>kube-apiserver: KMSv1
  kube-apiserver->>kube-apiserver: generate a dedicated DEK per object
  kube-apiserver->>kube-apiserver: encrypt Secret with DEK
  kube-apiserver->>KMS Plugin: encrypt DEK with KMS KEK
  KMS Plugin->>KMS Server: encrypt DEK
  KMS Server->>KMS Server: encrypt DEK with KEK
  KMS Plugin->>kube-apiserver: return encrypted DEK
  kube-apiserver->>etcd: store encrypted Secret and encrypted DEK
  kube-apiserver->>User or App: Secret created
```

```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
participant EncryptionConfiguration
participant KMS Plugin
end
participant KMS Server
autonumber
  User or App->>kube-apiserver: create Secret
  kube-apiserver->>EncryptionConfiguration: which provider?
  EncryptionConfiguration->>kube-apiserver: KMSv2
  opt if no existing DEK
    kube-apiserver->>kube-apiserver: generate a DEK
    kube-apiserver->>KMS Plugin: encrypt DEK with KMS KEK
    KMS Plugin->>KMS Server: encrypt DEK
    KMS Server->>KMS Server: encrypt DEK with KEK
    KMS Plugin->>kube-apiserver: return encrypted DEK
    kube-apiserver->>etcd: store encrypted DEK
  end
  kube-apiserver->>kube-apiserver: encrypt Secret with DEK
  kube-apiserver->>etcd: store encrypted Secret
  kube-apiserver->>User or App: Secret created
```


```mermaid
sequenceDiagram
participant User or App
box Control Plane
participant etcd
participant kube-apiserver
participant KMS Provider
participant KMS Plugin
end
participant KMS Server
autonumber
  User or App->>kube-apiserver: create Secret
  kube-apiserver->>KMS Provider: request to encrypt Secret
  Note left of KMS Provider: generate a DEK
  Note left of KMS Provider: encrypt Secret
  KMS Provider->>KMS Plugin: hand off the DEK for encryption
  KMS Plugin->>KMS Server: encrypt DEK with KEK from KMS
  KMS Plugin->>KMS Provider: return encrypted DEK with KID
  KMS Provider->>kube-apiserver: return encrypted DEK with KID
  kube-apiserver->>etcd: store Secrets and DEK both encrypted
```



```mermaid
sequenceDiagram
participant User
participant etcd
participant kube-apiserver
participant EncryptionConfiguration
autonumber
  User->>kube-apiserver: create Secret
  kube-apiserver->>EncryptionConfiguration: which provider?
  EncryptionConfiguration->>kube-apiserver: aesgcm
  kube-apiserver->>kube-apiserver: encrypt Secret with key
  kube-apiserver->>etcd: store encrypted Secret
```

```mermaid
sequenceDiagram
participant User
participant etcd
participant kube-apiserver
participant EncryptionConfiguration
participant KMS Plugin
participant KMS Server
autonumber
  User->>kube-apiserver: create Secret
  kube-apiserver->>EncryptionConfiguration: which provider?
  EncryptionConfiguration->>kube-apiserver: KMSv1
  kube-apiserver->>kube-apiserver: generate a dedicated DEK per object
  kube-apiserver->>kube-apiserver: encrypt Secret with DEK
  kube-apiserver->>KMS Plugin: encrypt DEK with KMS KEK
  KMS Plugin->>KMS Server: encrypt DEK
  KMS Server->>KMS Server: encrypt DEK with KEK
  KMS Plugin->>kube-apiserver: return encrypted DEK
  kube-apiserver->>etcd: store encrypted Secret and encrypted DEK
```

```mermaid
sequenceDiagram
participant User
participant etcd
participant kube-apiserver
participant EncryptionConfiguration
participant KMS Plugin
participant KMS Server
autonumber
  User->>kube-apiserver: create Secret
  kube-apiserver->>EncryptionConfiguration: which provider?
  EncryptionConfiguration->>kube-apiserver: KMSv2
  opt if no DEK
    kube-apiserver->>kube-apiserver: generate a DEK
    kube-apiserver->>KMS Plugin: encrypt DEK with KMS KEK
    KMS Plugin->>KMS Server: encrypt DEK
    KMS Server->>KMS Server: encrypt DEK with KEK
    KMS Plugin->>kube-apiserver: return encrypted DEK
    kube-apiserver->>etcd: store encrypted DEK
  end
  kube-apiserver->>kube-apiserver: encrypt Secret with DEK
  kube-apiserver->>etcd: store encrypted Secret
```

### Software Components

#### Trousseau

Trousseau deploys as a container on the Control Plane node. It will create a temporary Unix socket instantiating a gRPC server that the KMS Provider component from Kubernetes API Server will be able to communicate with.  

#### Vault Agent

Trousseau leverages a sidecar with the HashiCorp Vault Agent to recover the Transit Engine parameters. This allows to avoid direct exposure to the Encryption Transit Engine. 

#### Vault 

Trousseau requires a KMS like HashiCorp Vault to provide access to a Transit Encryption Engine for the Kubernetes KMS Provider. 

### Kubernetes Components

#### ServiceAccount

A ServiceAccount is created to using a certificate token used for a mutual authentication with the HashiCorp Vault instance. This is leverage by the sidecar to recover the Transit Engine parameters. 

#### ConfigMap

A ConfigMap is used by the HashiCorp Vault Agent sidecar to hand over the Encryption Transit Engine parameters to Trousseau. The following parameters are expected:

- the Transit Engine Endpoint
- the Transit Engine Key name
- the Transit Engine dedicated access Token 

#### DaemonSet 

Trousseau deploys as a DaemonSet to guarantee that every Control Plane node will have a running instance with its gRPC server addressing any potential partitioning that could occur during the Control Plane lifetime. 

## Trousseau Architecture Workflow 

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

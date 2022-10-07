
Summary   

Trousseau is an open-source Plugin for the [Kubernetes KMS provider](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/), securing data sensitve objects within etcd, like for Secret and ConfigMap objects, leveraging an external Key Management Service (KMS) server.   

Trousseau is designed to be both a framework and a usable plugin to interact with any KMS server like: 

| Version | AWS KMS | Azure KMS | HashiCorp Vault |
|-----------|---------|-----------|-----------------|
| v1.x | :material-close: | :material-close: | :material-check-all: |
| v2.x | :material-check-all: | :material-check-all: | :material-check-all: |

--- 
Why   

Kubernetes platform users are all facing the very same question: how to handle Secrets?

While using etcd to store API object definitions and their state, the Kubernetes secret sensitive data fields are encoded in base64 and shipped as-is into the key value store database. Even if the filesystem on which etcd runs is encrypted, the accessing Secrets from the Kubernetes API is not.

Not always considered, Secrets are used by both platform components and applications. While the application secrets could be stored within an external Key Management Service (KMS), the platform components can't as they leverages the native Kubernetes API for Secrets management.   
This design pattern supports an open integration architecture along with standardization, scalability, and resilience.

To support the above statment, let's consider the two caterogies:
- applications: if the front-end app is not able to access the external KMS, the front-end will failed to access its back-end due to the lack of credentials. It will not impact a running application, but it will be impacting when the app is scheduled or rescheduled. 
- Kubernetes components: if a cloud native storage stores its data encryption keys within an external KMS, all CRUD operations will be failing due to the lack of credentials impacting all the applications, logging, metrics, container registries, ... running on the platform. 

To address the above conundrum, projects like [External Secret Operator](https://external-secrets.io/) allows to synchronized given credentials from KMS server within the Kubernetes platform, aka within the etcd, style encoded in base64. Still, the in-platform secrets are not protected.

---
How   

By leveraging the Kubernetes KMS provider, data sensitive objects, like Secrets and ConfigMaps, can be encrypted using Trousseau as a PLugin to an external KMS server providing an improved entropy. These operations are seamless as they are using the native Kubernetes API.


Summary   

Trousseau is an open-source project, based on the [Kubernetes KMS provider design](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/), securing data sensitve objects, like Secrets and ConfigMap, within the Kubernetes etcd by using an envelope encryption scheme.   

Trousseau is designed to be both a framework and a usable plugin to interact with any KMS provider (see [release notes](trousseau/release_notes.md)).

--- 
Why   

Kubernetes platform users are all facing the very same question: how do we handle Secrets?

While there are significant efforts to improve Kubernetes component layers, [the state of Secret Management isn't receiving much interest](https://archive.fosdem.org/2021/schedule/event/kubernetes_secret_management/). Using etcd to store API object definition & states, Kubernetes secrets are encoded in base64 and shipped as-is into the key value store database. Even if the filesystem on which etcd runs is encrypted, the etcd entries are not.

Instead of leveraging the native Kubernetes way to manage secrets, commercial and open source solutions solve this design flaw by leveraging different approaches all using different toolsets or practices. This leads to training and maintaining niche skills and tools increasing the cost and complexity of Kubernetes day 0, 1 and 2.

---
How   

By leveraging the native Kubernetes KMS provider framework, data sensitive fields can be secured using an envelope encryption scheme to encrypt the payload within the platform. 

Once deployed, Trousseau will enable seamless secret management using the native Kubernetes API and kubectl CLI with an existing Key Management Service (KMS) provider.

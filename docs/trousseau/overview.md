
Trousseau installation deploys and maintains its component as container using native Kubernetes API and the KMS (Key Management Service) tooling.   

The following workflow shows the installation steps:

| # | Activity | Reference      |
|---|----------|----------------|
| 1 | Deploy a KMS | e.g. for [HashiCorp Vault](https://docs.trousseau.io/trousseau/setup-hashicorp-vault/) |
| 2 | Configure a KMS Encryption Key Engine | e.g. [HashiCorp Vault](https://docs.trousseau.io/trousseau/v1/deployment/#setup-vault-transit-engine) |
| 3 | Deploy Trousseau DaemonSet | e.g. [Deploy Trousseau](https://docs.trousseau.io/trousseau/v1/deployment/#customized-trousseaus-daemonset) |
| 4 | Enable Trousseau KMS Plugin | e.g. [Vanilla Kubernetes](https://docs.trousseau.io/trousseau/setup-vanilla/) |
| 5 | Verify the Setup | e.g. [Walkthrough](https://docs.trousseau.io/trousseau/verify-setup/) | 
| 6 | Protect existing Secrets with Trousseau | e.g. [Replace existing Secrets](https://docs.trousseau.io/trousseau/operations/) |

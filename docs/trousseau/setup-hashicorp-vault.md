
## Setup a dev/test HashiCorp Vault

!!! warning 
    This HashiCorp Vault deployment is sufficent for a dev/test environment ([Getting Started](https://learn.hashicorp.com/tutorials/vault/getting-started-dev-server?in=vault/getting-started)). 
    
    **However, this setup is ephemeral and restarting ```vault``` will spawn a clean empty instance.**
    
    For a persistent/production deployment, consider either the [Consul](https://learn.hashicorp.com/collections/vault/day-one-consul) or [integrated storage](https://learn.hashicorp.com/collections/vault/day-one-raft) deployment, or reaching out to HashiCorp for support. 
    

Start a dev/test Vault instance with a customer root token:
```bash
vault server -dev -dev-root-token-id=trousseau-demo -dev-listen-address 0.0.0.0:8200 -log-level=debug
```

Expected output of the console from starting HashiCorp Vault:
``` hl_lines="21 26 31 32"
2022-03-05T10:35:10.760Z [INFO]  core.cluster-listener.tcp: starting listener: listener_address=0.0.0.0:8201
2022-03-05T10:35:10.760Z [INFO]  core.cluster-listener: serving cluster requests: cluster_listen_address=[::]:8201
2022-03-05T10:35:10.760Z [INFO]  core: post-unseal setup starting
2022-03-05T10:35:10.761Z [INFO]  core: loaded wrapping token key
2022-03-05T10:35:10.761Z [INFO]  core: successfully setup plugin catalog: plugin-directory="\"\""
2022-03-05T10:35:10.762Z [INFO]  core: successfully mounted backend: type=system path=sys/
2022-03-05T10:35:10.762Z [INFO]  core: successfully mounted backend: type=identity path=identity/
2022-03-05T10:35:10.762Z [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2022-03-05T10:35:10.765Z [INFO]  core: successfully enabled credential backend: type=token path=token/
2022-03-05T10:35:10.765Z [INFO]  rollback: starting rollback manager
2022-03-05T10:35:10.765Z [INFO]  core: restoring leases
2022-03-05T10:35:10.765Z [INFO]  expiration: lease restore complete
2022-03-05T10:35:10.765Z [INFO]  identity: entities restored
2022-03-05T10:35:10.765Z [INFO]  identity: groups restored
2022-03-05T10:35:10.766Z [INFO]  core: post-unseal setup complete
2022-03-05T10:35:10.766Z [INFO]  core: vault is unsealed
2022-03-05T10:35:10.769Z [INFO]  core: successful mount: namespace="\"\"" path=secret/ type=kv
2022-03-05T10:35:10.779Z [INFO]  secrets.kv.kv_42530b65: collecting keys to upgrade
2022-03-05T10:35:10.779Z [INFO]  secrets.kv.kv_42530b65: done collecting keys: num_keys=1
2022-03-05T10:35:10.779Z [INFO]  secrets.kv.kv_42530b65: upgrading keys finished
WARNING! dev mode is enabled! In this mode, Vault runs entirely in-memory
and starts unsealed with a single unseal key. The root token is already
authenticated to the CLI, so you can immediately begin using Vault.

You may need to set the following environment variable:

    $ export VAULT_ADDR='http://0.0.0.0:8200'

The unseal key and root token are displayed below in case you want to
seal/unseal the Vault or re-authenticate.

Unseal Key: 2c8NWfkyJg+J5Xg8xauuqlFVIM9XVI/R+/IG/YaBcEs=
Root Token: trousseau-demo

Development mode should NOT be used in production installations!
```

Export the environment variables to access the Vault service via API or CLI:
```bash
export VAULT_NAMESPACE=admin
export VAULT_TOKEN="trousseau-demo"
```

Verify access to Vault instance:
```bash
vault status 
```

```bash
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    1
Threshold       1
Version         1.9.3
Storage Type    inmem
Cluster Name    vault-cluster-caa9ea4c
Cluster ID      701cb42f-adfc-52aa-2a5a-53776fed0138
HA Enabled      false
```

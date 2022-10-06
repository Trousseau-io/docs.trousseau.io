
!!! note "HashiCorp Vault endpoint"
    This guide uses as Vault endpoint the FQDN ```tdevhvc-01.trousseau.io```.  
    Change as appropriate for your environment.  


### Kubernetes ServiceAccount

Create ServiceAccount
```
kubectl -n kube-system create serviceaccount trousseau-vault-auth
```

!!! info "from Kubernetes 1.24+"
    As of Kubernetes 1.24+, creating a ServiceAccount will not auto-generate the token and related secret. To create the token: 
    ```bash 
    kubectl apply -f trousseau-vault-auth-secret.yml 
    ```    
    
    ```YAML title="trousseau-vault-auth-secret.yml"
    --8<-- "trousseau/files/trousseau-vault-auth-secret.yml"
    ```
    
    Verify the ServiceAccount token creation:
    ```bash 
    kubectl -n kube-system describe secrets trousseau-vault-auth 
    ```
    
    ```
    Name:         trousseau-vault-auth
    Namespace:    kube-system
    Labels:       <none>
    Annotations:  kubernetes.io/service-account.name: trousseau-vault-auth
                 kubernetes.io/service-account.uid: aa9fe853-29a7-4f0d-a247-0e6df10925f7

    Type:  kubernetes.io/service-account-token

    Data
    ====
    ca.crt:     566 bytes
    namespace:  11 bytes
    token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Illybldqd2xaRTZNRl95RXpoZzFiTFVRWlVWLUpjZkdraHlSTDVxTmZfX1EifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJ2YXVsdC1hdXRoLXNlY3JldCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJ2YXVsdC1hdXRoIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiYWE5ZmU4NTMtMjlhNy00ZjBkLWEyNDctMGU2ZGYxMDkyNWY3Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOnZhdWx0LWF1dGgifQ.iD7-LOvvKRuNxVkr7P-bMd3x4c9g65LMhRDNoj8oAgnMq08_RkZrEXKWRCXiQ2h7ucNlop6DC17Hnp5cppbbcnSPDwOUV9iN2Y4I9yKmVOW0pv_f4Co52HmToswMElEaBj3l9LPzPiHG-QlTGPDoXX5j3mcj7Rd53UO7ep08CkvUZGFxInSNvuTkJScOda1_WQxRXLyB3AMW-hPfVcejsCI-6Lnea31YFFJ_WqO_mL5LTG92c4eOk5bF9i7sFkbLU8GvEWNzXytTSLYZ6J2J_sL1W8Eq8vfkwHhMu9dtEKcHoe3U0MRjP6ZVvsTSlcrQYIEUXmRlMLlofZzwkfSNpw
    ```
   
   Verify the link between the ServiceAccount and token:
   ```bash 
   kubectl -n kube-system describe sa trousseau-vault-auth
   ```
   ```
   Name:                trousseau-vault-auth
   Namespace:           kube-system
   Labels:              <none>
   Annotations:         <none>
   Image pull secrets:  <none>
   Mountable secrets:   <none>
   Tokens:              trousseau-vault-auth
   Events:              <none>
   ```

Apply RBAC rules to the ServiceAccount  
```
kubectl apply -f trousseau-vault-auth-rbac.yml
```

```YAML title="trousseau-vault-auth-rbac.yml"
--8<-- "trousseau/files/trousseau-vault-auth-rbac.yml"
```

### Setup Vault Kubernetes Auth
Gather the Kubernetes ServiceAccount details to configure the Vault Auth for Kubernetes
```
export VAULT_SA_NAME=$(kubectl -n kube-system get sa trousseau-vault-auth \
    --output jsonpath="{.secrets[*]['name']}")
```

```
export SA_JWT_TOKEN=$(kubectl -n kube-system get secret $VAULT_SA_NAME \
    --output 'go-template={{ .data.token }}' | base64 --decode)
```

```
export SA_CA_CRT=$(kubectl -n kube-system config view --raw --minify --flatten \
    --output 'jsonpath={.clusters[].cluster.certificate-authority-data}' | base64 --decode)
```

```
export K8S_HOST=$(kubectl config view --raw --minify --flatten \
    --output 'jsonpath={.clusters[].cluster.server}')
```

Enable Kubernetes Auth method in HashiCorp Vault
```
vault auth enable kubernetes
```

Generate the command with the above gathered details

```
echo vault write auth/kubernetes/config token_reviewer_jwt="\"$SA_JWT_TOKEN\"" kubernetes_ca_cert="\"$SA_CA_CRT\"" issuer="\"https://kubernetes.default.svc.cluster.local\"" kubernetes_host="\"$K8S_HOST\""
```

The expected output from the above ```echo``` is the command to run on the Vault CLI to enable the ServiceAccount to successfuly authenticate with Vault:
```
vault write auth/kubernetes/config token_reviewer_jwt="eyJhbGciOiJSUzI1NiIsImtpZCI6ImJoOXRmMk1WaDNTN3R3V1lhZ1ZZTm1DenBXRGR3dXN3ckRuY2prSUxfUDQifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2ViudzjZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJ2YXVsdC1hdXRoLXRva2VuLWhqY2IyIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6InZhdWx0LWF1dGgiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJhNWFiYjI1Ni1kNjA5LTRiMGUtYmQ4MS00Y2I3N2Q4YTRhOWUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06dmF1bHQtYXV0aCJ9.kY2U--XAReFmcdUO-aLLiebdDsnyViPdkM3godbJGkaual_SZUIUXhjNv4adyb5UIRXsvfz_-pXR4Qnr3nv4XxGO59DTyGmStl01VTnY9knYdxZ1gh5SpezsJ5kmOy5NcsFumyCzQTpBCJKP6dXDqtGvm4XvH1Cs_f08fS9RhcZW7qRtuG2A7qqi64FayMsJlXW7y0qs_fOP92gmgpSDhTPcTzzD2XX5M_fDwj7Y-yMOSQrbaqd2kRqet9XubqcASaKwU_YWZ2BaIqX0IG3fErP1AXCg8JjEdgGkcJqF4aFU7DUMOM-Yh73FBO8Kc2WiiHGfrPMbazaXEoFOSQSuOg" kubernetes_ca_cert="-----BEGIN CERTIFICATE-----
MIIBeTCCAR+gAwIBAgJDRPNKBggqhkjOPQQDAjAkMSIwIAYDVQQDDBlya2UyLXNl
cnZlci1jYUAxNjUxMjUxNjc1MB4XDTIyMDQyOTE3MDExNVoXDTMyMDQyNjE3MDEx
NVowJDEiMCAGA1UEAwwZcmtlMi1zZXJ2ZXItY2FAMTY1MTI1MTY3NTBZMBMGByqG
SM49AgEGCCqGSM49AwEHA0IABKi3hk4RAaE117QT8snLG9sFLe4s0OWLOgh+gOIs
x7WYKi6jp+OWLjs22AbyZV8z29GseRFNGkG6ChH90ANq5oWjQjBAMA4GA1UdDwEB
/wQEAwICpDAPBgNVHRMBAf8EBTADAQH/MB0GA1UdDgQWBBQp7m1LRyIolZUEKnDm
uEZvo1GjwzAKBggqhkjOPQQDAgNIADBFAiBq0052efxOPwieUB3T6wA5RkVKVdDd
ZAPi0FD2284iDQIhAJWPmpj6FixOe7I8kG0vij8rbN0N/+R8CzTrnNZ5Ioit
-----END CERTIFICATE-----" issuer="https://kubernetes.default.svc.cluster.local" kubernetes_host="https://127.0.0.1:6443"
```

!!! warning 
    * the quotes are required to avoid partial writings of the configuration with Vault 
    * the ```kubernetes_host``` might return ```https://127.0.0.1:6443``` resulting to a failure when Vault will dialback to the Kubernetes cluster to do an API call for the ```tokenreviews```. Export manually the external IP/FQDN Kubernetes API endpoint (like ```export K8S_HOST=https://FQDN:6443```). 
    * the ```issuer``` could be different than ```https://kubernetes.default.svc.cluster.local``` when a Kubernetes cluster was set up with a custom ```service-account-issuer``` (see [Kubernetes documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)).   
    The discovery can be done following this [HashiCorp article](https://www.vaultproject.io/docs/auth/kubernetes#discovering-the-service-account-issuer).


### Setup Vault Transit engine

Enable and configure a Transit engine (Security team)
```
vault secrets enable transit
vault write -f transit/keys/trousseau-kms
```

Create a Transit access policy 
```
vault policy write trousseau-transit-ro -<<EOF
path "transit/encrypt/trousseau-kms" {
   capabilities = [ "update" ]
}
path "transit/decrypt/trousseau-kms" {
   capabilities = [ "update" ]
}
EOF
```

Create a dedicated Token to access the Transit engine
```
vault token create -policy=trousseau-transit-ro
```

Expected output should be similar to:
```
Key                  Value
---                  -----
token                hvs.CAESILoUyuj8STPYKR4AGhaCJylJbkOkmlXlU8pZukoQKc_bGh4KHGh2cy5vQkpnc2g0RVNFZEpsWTA0SWlSNDBxWDQ
token_accessor       BBTat50bsupNqAQNLTXXRhr7
token_duration       768h
token_renewable      true
token_policies       ["default" "trousseau-transit-ro"]
identity_policies    []
policies             ["default" "trousseau-transit-ro"]
```

Export the Token:
```
export TROUSSEAU_TOKEN="hvs.CAESILoUyuj8STPYKR4AGhaCJylJbkOkmlXlU8pZukoQKc_bGh4KHGh2cy5vQkpnc2g0RVNFZEpsWTA0SWlSNDBxWDQ"
```

Create a key-value policy store for Trousseau 
```
vault policy write trousseau-kv-ro - <<EOF
path "secret/data/trousseau/*" {
    capabilities = ["read", "list"]
}
EOF
```

Inject Trousseau configuration parameters within HashiCorp Vault key-value store 
```
vault kv put /secret/trousseau/config transitkeyname=trousseau-kms \
vaultaddress=$VAULT_ADDR vaulttoken=$TROUSSEAU_TOKEN \
 ttl=30s
```

Create a link between the ServiceAccount, namespace, and policies in Vault
```
vault write auth/kubernetes/role/trousseau \
        bound_service_account_names=trousseau-vault-auth \
        bound_service_account_namespaces=kube-system \
        policies=trousseau-kv-ro \
        ttl=24h
```

### Vault Agent ConfigMap

Apply Trousseau's ConfigMap
```
kubectl apply -f trousseau-vault-configmap.yml
```

```YAML title="trousseau-vault-configmap.yml"
--8<-- "trousseau/files/trousseau-vault-configmap.yml"
```

### Customized Trousseau's DaemonSet

```YAML title="trousseau-vault-daemonset.yml"
--8<-- "trousseau/files/trousseau-vault-daemonset.yml"
```

1. :material-information: update tag to the desired version
2. :material-information: set the Vault endpoint URL up
3. :material-information: update tag to the desired version
4. :material-information: uncomment with Vault Enterprise and set a namespace in `value` parameter
5. :material-information: set to false in production with signed TLS certificate  



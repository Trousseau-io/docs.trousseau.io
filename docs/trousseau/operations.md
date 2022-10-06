
## Replace existing Secrets
The ```secret-pre-deploy``` was create prior to the deployment of Trousseau and is unsafe as shown within the previous section.

Replace an unsafe secret with a safe one
```
kubectl get secrets secret-pre-deploy -o json |kubectl replace -f -
```

Verify if the secret is now encrypted
```
kubectl -n kube-system exec etcd-tdevk8s-01.trousseau.io -- sh -c "ETCDCTL_ENDPOINTS='https://127.0.0.1:2379' ETCDCTL_CACERT='/var/lib/rancher/rke2/server/tls/etcd/server-ca.crt' ETCDCTL_CERT='/var/lib/rancher/rke2/server/tls/etcd/server-client.crt' ETCDCTL_KEY='/var/lib/rancher/rke2/server/tls/etcd/server-client.key' ETCDCTL_API=3 etcdctl get /registry/secrets/default/secret-pre-deploy" | hexdump -C
```

The output should expose a modified header and an encrypted payload
``` hl_lines="1 2 3 4 5"
00000000  2f 72 65 67 69 73 74 72  79 2f 73 65 63 72 65 74  |/registry/secret|
00000010  73 2f 64 65 66 61 75 6c  74 2f 73 65 63 72 65 74  |s/default/secret|
00000020  2d 70 72 65 2d 64 65 70  6c 6f 79 0a 6b 38 73 3a  |-pre-deploy.k8s:|
00000030  65 6e 63 3a 6b 6d 73 3a  76 31 3a 76 61 75 6c 74  |enc:kms:v1:vault|
00000040  70 72 6f 76 69 64 65 72  3a 00 59 76 61 75 6c 74  |provider:.Yvault|
00000050  3a 76 31 3a 63 70 71 6f  67 55 53 79 5a 31 62 2b  |:v1:cpqogUSyZ1b+|
00000060  30 78 7a 67 30 6b 5a 33  73 53 43 2b 6e 6e 6e 65  |0xzg0kZ3sSC+nnne|
00000070  33 53 57 4a 78 74 34 4c  37 79 45 4e 69 77 71 4a  |3SWJxt4L7yENiwqJ|
00000080  39 6b 54 37 47 59 70 45  30 4a 47 2f 68 66 54 4e  |9kT7GYpE0JG/hfTN|
00000090  65 74 52 43 76 57 30 49  62 66 72 71 73 53 4c 2f  |etRCvW0IbfrqsSL/|
000000a0  62 35 63 62 91 8b fd 7f  f4 33 84 0d fb 9f 86 1f  |b5cb.....3......|
000000b0  c4 40 0a 9f e2 ea c8 ce  a5 21 b6 d6 a5 8b 1f 77  |.@.......!.....w|
000000c0  35 75 df df 2f f1 99 e3  cb fb c5 24 e5 21 91 91  |5u../......$.!..|
000000d0  99 87 8b 52 d9 6d 07 22  99 29 36 4c 5f e2 6d 58  |...R.m.".)6L_.mX|
000000e0  a7 dc 30 67 4d d3 57 23  f9 a3 90 78 5b 28 11 ee  |..0gM.W#...x[(..|
000000f0  0a 08 c2 00 4e af 93 85  f4 51 c8 68 9a 3a 6d 86  |....N....Q.h.:m.|
00000100  b0 7e a3 d9 18 5d f0 fb  52 9f 30 1f 1a e0 78 32  |.~...]..R.0...x2|
00000110  22 87 75 e5 1b 7d 8a de  3f bf f5 92 76 2f 41 41  |".u..}..?...v/AA|
00000120  fb b8 1f 24 ec 8b 6a 30  4e 31 55 b6 b1 76 bd c3  |...$..j0N1U..v..|
00000130  dc 7a 06 96 51 45 31 67  1b 5a c6 fc 9f 7c 81 58  |.z..QE1g.Z...|.X|
00000140  56 83 29 dc b0 33 71 f3  c8 85 16 1f df 59 20 b5  |V.)..3q......Y .|
00000150  28 ec e1 0f 7b ac d4 fc  7d af 51 b7 f5 6d d6 92  |(...{...}.Q..m..|
00000160  05 4b 17 27 e2 8d 64 7a  7a 3e 16 54 65 b5 a2 fb  |.K.'..dzz>.Te...|
00000170  7b 44 c4 d2 2e 7f b2 71  ea 32 19 c6 12 7e 34 b5  |{D.....q.2...~4.|
00000180  1e f2 5b e9 ea d9 c1 9c  8b 14 56 36 f8 a9 30 c6  |..[.......V6..0.|
00000190  39 b3 1e 14 a7 8a 79 61  85 83 a7 7f 50 57 6d 09  |9.....ya....PWm.|
000001a0  e6 0a e2 d4 c6 56 46 14  c5 66 70 f7 36 64 b5 6f  |.....VF..fp.6d.o|
000001b0  a0 d7 63 5a 0a                                    |..cZ.|
000001b5
```

!!! note "Replace all previous secrets"
    The above method could be used to replace all secrets created prior to Trousseau deployment by executing the following variation of the command:   
    ```
    kubectl get secrets --all-namespaces -o json | kubectl replace -f -
    ```


The below approach is to verify straight out of the ETCD instances that the secret payloads are encrypted.

Create a secret
```
kubectl create secret generic secret-post-deploy -n default --from-literal=mykey=mydata
```

Verify if secrets are encrypted or not. The below example points to ```etcd-tdevk8s-01.trousseau.io``` as one of the etcd pod name within the Kubernetes cluster.

Verify the first secret ```secret-pre-deploy```
```
kubectl -n kube-system exec etcd-tdevk8s-01.trousseau.io -- sh -c "ETCDCTL_ENDPOINTS='https://127.0.0.1:2379' ETCDCTL_CACERT='/var/lib/rancher/rke2/server/tls/etcd/server-ca.crt' ETCDCTL_CERT='/var/lib/rancher/rke2/server/tls/etcd/server-client.crt' ETCDCTL_KEY='/var/lib/rancher/rke2/server/tls/etcd/server-client.key' ETCDCTL_API=3 etcdctl get /registry/secrets/default/secret-pre-deploy" | hexdump -C
```

The output should expose the Secret path with the etcd registery and the clear text key-values
``` hl_lines="1 2 3 4 17"
00000000  2f 72 65 67 69 73 74 72  79 2f 73 65 63 72 65 74  |/registry/secret|
00000010  73 2f 64 65 66 61 75 6c  74 2f 73 65 63 72 65 74  |s/default/secret|
00000020  2d 70 72 65 2d 64 65 70  6c 6f 79 0a 6b 38 73 00  |-pre-deploy.k8s.|
00000030  0a 0c 0a 02 76 31 12 06  53 65 63 72 65 74 12 d7  |....v1..Secret..|
00000040  01 0a bb 01 0a 11 73 65  63 72 65 74 2d 70 72 65  |......secret-pre|
00000050  2d 64 65 70 6c 6f 79 12  00 1a 07 64 65 66 61 75  |-deploy....defau|
00000060  6c 74 22 00 2a 24 62 64  32 65 30 66 62 37 2d 36  |lt".*$bd2e0fb7-6|
00000070  37 63 62 2d 34 63 65 35  2d 61 34 39 62 2d 65 37  |7cb-4ce5-a49b-e7|
00000080  38 65 39 30 33 65 36 65  32 38 32 00 38 00 42 08  |8e903e6e282.8.B.|
00000090  08 a3 a8 b4 93 06 10 00  7a 00 8a 01 62 0a 0e 6b  |........z...b..k|
000000a0  75 62 65 63 74 6c 2d 63  72 65 61 74 65 12 06 55  |ubectl-create..U|
000000b0  70 64 61 74 65 1a 02 76  31 22 08 08 a3 a8 b4 93  |pdate..v1"......|
000000c0  06 10 00 32 08 46 69 65  6c 64 73 56 31 3a 2e 0a  |...2.FieldsV1:..|
000000d0  2c 7b 22 66 3a 64 61 74  61 22 3a 7b 22 2e 22 3a  |,{"f:data":{".":|
000000e0  7b 7d 2c 22 66 3a 6d 79  6b 65 79 22 3a 7b 7d 7d  |{},"f:mykey":{}}|
000000f0  2c 22 66 3a 74 79 70 65  22 3a 7b 7d 7d 42 00 12  |,"f:type":{}}B..|
00000100  0f 0a 05 6d 79 6b 65 79  12 06 6d 79 64 61 74 61  |...mykey..mydata|
00000110  1a 06 4f 70 61 71 75 65  1a 00 22 00 0a           |..Opaque.."..|
0000011d
```

Verify the first secret ```secret-post-deploy```
```
kubectl -n kube-system exec etcd-tdevk8s-01.trousseau.io -- sh -c "ETCDCTL_ENDPOINTS='https://127.0.0.1:2379' ETCDCTL_CACERT='/var/lib/rancher/rke2/server/tls/etcd/server-ca.crt' ETCDCTL_CERT='/var/lib/rancher/rke2/server/tls/etcd/server-client.crt' ETCDCTL_KEY='/var/lib/rancher/rke2/server/tls/etcd/server-client.key' ETCDCTL_API=3 etcdctl get /registry/secrets/default/secret-post-deploy" | hexdump -C
```

The output should expose a modified header and an encrypted payload
``` hl_lines="1 2 3 4 5"
00000000  2f 72 65 67 69 73 74 72  79 2f 73 65 63 72 65 74  |/registry/secret|
00000010  73 2f 64 65 66 61 75 6c  74 2f 73 65 63 72 65 74  |s/default/secret|
00000020  2d 70 6f 73 74 2d 64 65  70 6c 6f 79 0a 6b 38 73  |-post-deploy.k8s|
00000030  3a 65 6e 63 3a 6b 6d 73  3a 76 31 3a 76 61 75 6c  |:enc:kms:v1:vaul|
00000040  74 70 72 6f 76 69 64 65  72 3a 00 59 76 61 75 6c  |tprovider:.Yvaul|
00000050  74 3a 76 31 3a 52 2b 70  6a 65 65 7a 45 63 70 41  |t:v1:R+pjeezEcpA|
00000060  45 4c 47 69 4e 2f 4b 42  44 54 73 68 72 79 49 76  |ELGiN/KBDTshryIv|
00000070  55 61 75 30 2f 4a 61 75  71 34 5a 2f 66 2f 68 63  |Uau0/Jauq4Z/f/hc|
00000080  35 50 75 4b 68 58 34 67  4e 67 54 79 43 7a 6f 69  |5PuKhX4gNgTyCzoi|
00000090  46 55 30 55 45 54 45 36  33 70 76 51 4c 46 69 50  |FU0UETE63pvQLFiP|
000000a0  47 79 41 4b 45 4d c3 65  18 a3 0b 93 10 43 f5 43  |GyAKEM.e.....C.C|
000000b0  5f 1a 7c 7a 9b ba ae cf  30 55 39 77 1b b1 dc 7d  |_.|z....0U9w...}|
000000c0  1a 74 45 d8 8e bb 7a f3  3d 75 e1 a2 4c 05 2a 01  |.tE...z.=u..L.*.|
000000d0  22 82 70 b8 ef 17 9f 9b  e6 3a 80 60 c6 a0 fd 61  |".p......:.`...a|
000000e0  4c 14 ac ee 1e c0 55 f5  00 df 6a 07 cf 8d 52 0a  |L.....U...j...R.|
000000f0  79 5a 99 42 8b ea a1 f0  26 2a fd dc ae 3d 8c 2c  |yZ.B....&*...=.,|
00000100  3d 9d 03 5b 3e cc 18 f4  20 7f 6a 3d 60 9e 20 c3  |=..[>... .j=`. .|
00000110  dd cb 5b 62 90 cb 31 4c  b1 1d 1b 2a 05 cf 77 29  |..[b..1L...*..w)|
00000120  46 39 65 f1 b0 d8 bc 3e  db 58 3c c1 23 4d 64 b5  |F9e....>.X<.#Md.|
00000130  f7 88 7b 01 f2 ca 10 ec  ad 3b ec ca 73 9e ae 21  |..{......;..s..!|
00000140  30 2a e0 ab 96 a5 65 28  fe e3 27 1e 3d 07 9e 1b  |0*....e(..'.=...|
00000150  84 8d da a9 0f 23 69 92  64 c3 b2 e6 c6 8d db 4d  |.....#i.d......M|
00000160  0a 5e 3e e1 c7 cd 52 58  0a 6b b4 dc c4 97 10 16  |.^>...RX.k......|
00000170  09 ea 8b d6 b9 96 0d 19  60 01 ed f0 01 12 5c f1  |........`.....\.|
00000180  68 99 d0 4b eb 3a 29 6b  ab 85 6e ae cc 22 e7 cd  |h..K.:)k..n.."..|
00000190  30 45 73 eb 1e c4 0b 67  55 ba 05 a7 2c 9e 92 97  |0Es....gU...,...|
000001a0  11 7f 0a 9a 72 78 b6 1e  61 14 4e ee 24 7e 03 29  |....rx..a.N.$~.)|
000001b0  85 1a 19 2c 93 0a                                 |...,..|
000001b6
```

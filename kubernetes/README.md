# Kubernetes setup

## Setting the configs and secrets in kubernetes configMap

```bash
    ./create_configs_secrets.sh
```

## Launch 3 nodes

```bash
    ./launch_kubernetes.sh
```

## Connecting the nodes together

port forward all the nodes in the cluster, in 3 different windows

```bash
kubectl port-forward svc/ethereumnode-poa-a 8645:8645
```

```bash
kubectl port-forward svc/ethereumnode-poa-b 8647:8647
```

```bash
kubectl port-forward svc/ethereumnode-poa-c 8649:8649
```

get the node A enode id:

```bash
curl --data '{"jsonrpc":"2.0","method":"parity_enode","params":[],"id":0}' -H "Content-Type: application/json" -X POST 127.0.0.1:8645

>> {"jsonrpc":"2.0","result":"enode://AAAAA@10.255.0.4:30303","id":0}
```

```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"parity_addReservedPeer","params":["enode://AAAAA@ethereumnode-poa-a:31303"],"id":0}' -H "Content-Type: application/json" 127.0.0.1:8647

>> {"jsonrpc":"2.0","result":true,"id":0}
```

now add the C peer to the B peer:

Get the C peer details

```bash
curl --data '{"jsonrpc":"2.0","method":"parity_enode","params":[],"id":0}' -H "Content-Type: application/json" -X POST 127.0.0.1:8649

>> {"jsonrpc":"2.0","result":"enode://CCCCC@10.255.0.4:30305","id":0}
```

```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"parity_addReservedPeer","params":["enode://CCCCC@ethereumnode-poa-c:31305"],"id":0}' -H "Content-Type: application/json" 127.0.0.1:8647

>> {"jsonrpc":"2.0","result":true,"id":0}
```

and if needed, now add the B peer to the C peer:
Get the B peer details

```bash
curl --data '{"jsonrpc":"2.0","method":"parity_enode","params":[],"id":2}' -H "Content-Type: application/json" -X POST 127.0.0.1:8547

>> {"jsonrpc":"2.0","result":"enode://BBBBB@10.255.0.4:30304","id":0}
```

```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"parity_addReservedPeer","params":["enode://BBBBB@poa_B:30304"],"id":0}' -H "Content-Type: application/json" 127.0.0.1:8549

>> {"jsonrpc":"2.0","result":true,"id":0}
```

You should now see the two peers synced (UI or logs) for peer A.s

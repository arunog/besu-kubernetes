
## Flow of the process:
```bash
cd ~/besu-kubernetes/kubectl/ibft2-test/
./deploy.sh

kubectl get services -A

kubectl logs -n besu validator1-0 | grep enode | less

enode://4826f9c05788be18bb65ea801560d303cdd502fa5ac8dcda92b34840cc4669b4976506a43c5fbf7e49f47bbb780f896dbd0027f76b0511bb1c76dbdeec29afeb@xx.xx.xx.xx:30303

nano ~/besu-kubernetes/kubectl/ibft2-test/newnode/besu-configmap.yaml
check validator1PubKey && validator1Ip UDP

kubectx test-aks-2

cd ~/besu-kubernetes/kubectl/ibft2-test/
./addNode.sh

kubectl logs -n besu node-0 | grep enode | less

enode://58a6b4578e07042e66bce0187bc3f58b484f62323d6eab5ffa7f50ab833b5de76a5269b5896d6f3b8b953a073f6fced1dc6b72388adf82c3dc4d27294b092ff7@xx.xx.xx.xx:30303

## Check net_peerCount
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545

## Force add peer Bootnode Cluster A to Observer Cluster B
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://4826f9c05788be18bb65ea801560d303cdd502fa5ac8dcda92b34840cc4669b4976506a43c5fbf7e49f47bbb780f896dbd0027f76b0511bb1c76dbdeec29afeb@xx.xx.xx.xx:30303"],"id":1}' http://xx.xx.xx.xx:8545
## Force add peer Observer Cluster B to Bootnode Cluster A
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://58a6b4578e07042e66bce0187bc3f58b484f62323d6eab5ffa7f50ab833b5de76a5269b5896d6f3b8b953a073f6fced1dc6b72388adf82c3dc4d27294b092ff7@xx.xx.xx.xx:30303"],"id":1}' http://xx.xx.xx.xx:8545

## Check net_peerCount
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545

## Create Besu Observer in Docker configuring Bootnode Cluster A as bootnode

docker run --name node-1 -d -p 8545:8545 -p 8546:8546 -p 9545:9545 -p 30303:30303 --mount type=bind,source=/home/sabraroot/test-k8s/,target=/opt/pantheon/node hyperledger/besu:latest --data-path=/opt/pantheon/node/data --genesis-file=/opt/pantheon/node/genesis.json --rpc-http-enabled --rpc-http-host=0.0.0.0 --host-whitelist="*" --rpc-http-cors-origins="all" --rpc-http-api=ETH,NET,IBFT,ADMIN,WEB3,PERM,EEA,PRIV --bootnodes=enode://4826f9c05788be18bb65ea801560d303cdd502fa5ac8dcda92b34840cc4669b4976506a43c5fbf7e49f47bbb780f896dbd0027f76b0511bb1c76dbdeec29afeb@xx.xx.xx.xx:30303 --p2p-host=0.0.0.0 --revert-reason-enabled=true --rpc-ws-enabled --rpc-ws-host=0.0.0.0 --rpc-ws-port=8546 --metrics-enabled --metrics-host=0.0.0.0 --metrics-port=9545 --logging=TRACE

docker logs node-1 | grep enode

enode://f5cbaca1105eaf72b9c82fa044cbd620607e2f891c9b4fd933299fc757fea2481da0dee3bc240e79b6c94edd6cce540f27f4d18102e723e7421967af5b2e4af2@xx.xx.xx.xx:30303

## Check net_peerCount
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545

## Force add peer Bootnode Cluster A to Docker observer
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://4826f9c05788be18bb65ea801560d303cdd502fa5ac8dcda92b34840cc4669b4976506a43c5fbf7e49f47bbb780f896dbd0027f76b0511bb1c76dbdeec29afeb@xx.xx.xx.xx:30303"],"id":1}' http://xx.xx.xx.xx:8545
## Force add peer Docker Observer to Bootnode Cluster A to 
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://f5cbaca1105eaf72b9c82fa044cbd620607e2f891c9b4fd933299fc757fea2481da0dee3bc240e79b6c94edd6cce540f27f4d18102e723e7421967af5b2e4af2@xx.xx.xx.xx:30303"],"id":1}' http://xx.xx.xx.xx:8545

## Check net_peerCount
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":51}' http://xx.xx.xx.xx:8545

## Force add peer Observer Cluster B to Docker Observer
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://58a6b4578e07042e66bce0187bc3f58b484f62323d6eab5ffa7f50ab833b5de76a5269b5896d6f3b8b953a073f6fced1dc6b72388adf82c3dc4d27294b092ff7@xx.xx.xx.xx:30303"],"id":1}' http://xx.xx.xx.xx:8545
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://f5cbaca1105eaf72b9c82fa044cbd620607e2f891c9b4fd933299fc757fea2481da0dee3bc240e79b6c94edd6cce540f27f4d18102e723e7421967af5b2e4af2@xx.xx.xx.xx:30303"],"id":1}' http://xx.xx.xx.xx:8545
```

​                            

#           renewing-kubernetes-certificates  1.15

## 1.verify the expiry time 

```
echo | openssl s_client -showcerts -connect 127.0.0.1:6443 -servername api 2>/dev/null | openssl x509 -noout -enddate
```

## 2.Manual renewal process

```
kubeadm alpha certs renew apiserver-kubelet-client
kubeadm alpha certs renew apiserver
kubeadm alpha certs renew front-proxy-client
```

## 3.create a configuration file in  current dir config.yaml

```
 kubeadm config view
```

```
apiVersion: kubeadm.k8s.io/v1beta2
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 192.168.7.100
  bindPort: 6443
```

## 4  back up config,renew config 

```
cp cd /etc/kubernetes{,.bak}

kubeadm init phase kubeconfig all --config=kube.yaml
```


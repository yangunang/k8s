#        kubernetes expired certificate   

######        version 1.9 

#### 1.**check all certificate expire date:**  <!--on master-->

```
find /etc/kubernetes/pki/ -type f -name "*.crt" -print|egrep -v 'ca.crt$'|xargs -L 1 -t  -i bash -c 'openssl x509  -noout -text -in {}|grep After'
```

#### 2.create a configuration file in  current dir config.yaml

```
apiVersion: kubeadm.k8s.io/v1alpha1
kind: MasterConfiguration
api:
  advertiseAddress: 192.168.3.30
  bindPort: 6443
kubernetesVersion: 1.9.3
```

use command:  kubeadm config view  ,get config info

####  3.Backup certs  && remove old certs  

`backup`

```
cp   /etc/kubernetes/pki/apiserver.key   backdir/ 
cp   /etc/kubernetes/pki/apiserver.crt   backdir/ 
cp   /etc/kubernetes/pki/apiserver-kubelet-client.crt  backdir/ 
cp   /etc/kubernetes/pki/apiserver-kubelet-client.key  backdir/ 
cp   /etc/kubernetes/pki/front-proxy-client.crt  backdir/ 
cp   /etc/kubernetes/pki/front-proxy-client.key  backdir/ 
```

`remove`

```
rm   /etc/kubernetes/pki/apiserver.key   
rm   /etc/kubernetes/pki/apiserver.crt  
rm   /etc/kubernetes/pki/apiserver-kubelet-client.crt  
rm   /etc/kubernetes/pki/apiserver-kubelet-client.key  
rm   /etc/kubernetes/pki/front-proxy-client.crt  
rm   /etc/kubernetes/pki/front-proxy-client.key  
```

#### 4.Create new certificates:

```
   kubeadm --config ./config.yaml  alpha phase certs apiserver
   kubeadm --config ./config.yaml  alpha phase certs apiserver-kubelet-client
   kubeadm --config ./config.yaml  alpha phase certs front-proxy-client
```

#### 5.Backup  configuration  && remove old configuration 

`backup`

```
cp /etc/kubernetes/admin.conf  backdir/
cp /etc/kubernetes/kubelet.conf  backdir/
cp /etc/kubernetes/controller-manager.conf  backdir/
cp /etc/kubernetes/scheduler.conf   backdir/
```

`remove`

```
rm /etc/kubernetes/admin.conf
rm /etc/kubernetes/kubelet.conf
rm /etc/kubernetes/controller-manager.conf
rm /etc/kubernetes/scheduler.conf 
```

#### 6.Generate new configuration files:

```
kubeadm --config ./config.yml alpha phase kubeconfig all
```

#### 7.Ensure that your kubectl service is using the correct configuration files:

```
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
export KUBECONFIG=.kube/config
```

#### 8.Reboot the Kubernetes master node.

#### 9.After the server restarts, check to ensure that the **kubelet** service is running:

```
systemctl status kubelet
```

#### 10.HA set other master

  copy the  <u>***step 4***</u>  generate certificates to other master 

  copy the  ***<u>step 2</u>***  generate config.yml to other master

  on other master run the ***<u>step 6</u>***  Generate new configuration files

  on other master run the  ***<u>step 7</u>***  copy kubectl use the correct configuration file

  end reboot other master 

####  11. on master you must create a new token 

```
kubeadm token create --print-join-command
```

#### 12  delete node 

```
kubeadmin reset
```

use ***<u>step 11</u>***  return command on nodes run ,then add to join to master 

join it 
# Container-threat-detection

**Enable on all nodes**

```bash
kubectl get ds runtime-reporter -n calico-system -o yaml | sed '/enable-tigera-runtime-security/d' | kubectl apply -f -
```

**Enable on specific nodes**

To enable container threat detection on a particular node, add the enable-tigera-runtime-security: t label to that node. For example:

```bash
kubectl label nodes <node-name> enable-tigera-runtime-security=t
```

**check running pods** 

Make sure that runtime-reporters pod is runing 

```basg
kubectl get pods -n calico-system
```

**change aggregation periot**

The default configuration of the runtime-reporter has an Aggregation period of 15  minutes [period: 15m].  
In order to expedite testing you may like to significantly reduce this to 15 seconds

```bash
kubectl -n calico-system edit daemonset.apps/runtime-reporter
```
Change the period: 15m to period: 15s 

Note: optionally you can remove 'AnyProcessInSamePodPrefix' as well to get the pod name in the alert but You'd also get a lot of reports and alerts as a result. 

**deploy a testing pod**

```bash
kubectl run multitool --image=wbitt/network-multitool
```

**Examples to test**

```bash
kubectl exec -it multitool -- bash
```

Linux-Administrative-Command

```bash
adduser joseph
```

Set-Linux-Capabilities

```bash
touch file.txt
setcap cap_net_raw+ep file.txt
 ```
 
 
 
 
 
 su -
 add user joseph
 chown joseph file.txt
  nmap -Pn -r -p 1-900 $POD_IP
 

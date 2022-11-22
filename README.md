# Container-threat-detection

Calico Cloud currently includes following detectors focused on the execution and privilege-escalation stages of cyberattacks and intrusions.
 
Privilege-Escalation: 
- Linux-Administrative-Command
- Set-Linux-Capabilities

Execution:
- Attack Tools
- Cryptomining Pool Hostname
- Curl or wget to Suspicious TLD
- DNS Attack Tool



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
Set-Linux-Capabilities

```bash
adduser joseph
su -
add user joseph
chown joseph file.txt
```

Set-Linux-Capabilities

```bash
touch file.txt
setcap cap_net_raw+ep file.txt
 ```
 
Attack Tools
```bash
nmap -Pn -r -p 1-900 $POD_IP
```

**Mitigate the risk of exploitation using security policy**

Once you get the alert and are sure this is not a legitimate activity, you may need to quarantine this pod using Calico security policy. 

A best practice is to always have quarantine policy preconfigured in each cluster  


```bash
apiVersion: projectcalico.org/v3
kind: GlobalNetworkPolicy
metadata:
  name: security.quarantine
spec:
  tier: security
  order: 100
  selector: quarantine == "true"
  ingress:
    - action: Log
    - action: Deny
  egress:
    - action: Log
    - action: Deny
  types:
    - Ingress
    - Egress
```

So you can easily quarantine the malicious workload by adding the label “quarantine=true", here is an example:

```bash
kubectl label pod maliciouspod quarantine=true
```
 

 

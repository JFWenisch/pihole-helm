
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
helm install pihole-jfwenisch --set webpassword=changeme --set ipaddress=192.168.1.9 --set timezone=Europe/Berlin --set piholedatamountpath=/pihole/data --set piholednsmasqdatamountpath=/pihole/dnsmasq .

```

Per default Europe/Berlin is set as timezone. If no webpassword is supplied a random password will be generated on startup


## Getting started
3 different services are created. One for dns on Port 53, one for dhcp on port 67 and one for the pihole webservice on port 80 and 443.
The services for dns and dhcp utilize [external IP](https://kubernetes.io/docs/tutorials/stateless-application/expose-external-ip-address/) to bind the ports to the host.

Run kubectl to view the created svc

```
PS E:\pihole-helm> kubectl get svc --all-namespaces
NAMESPACE     NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                           AGE
...
default       pihole-jfwenisch-dhcp       ClusterIP   10.152.183.197   10.0.0.6      67/UDP                                            5s
default       pihole-jfwenisch-dns        ClusterIP   10.152.183.149   10.0.0.6      53/TCP,53/UDP                                     5s
default       pihole-jfwenisch-web        ClusterIP   10.152.183.196   <none>        80/TCP,443/TCP                                    5s

```

To open the webui you can either change the port type of the pihole-jfwenisch-web svc to nodePort, use port forwarding or create an Ingress object if you are using ingress. If you have ingress installed, an ingress object is deployed so you can just visit http://10.0.0.6/admin (Change the IP to your set IPaddress). Otherwise to get started you can use kubectl port-forward to bind the ports to your localhost. 

```
PS E:\pihole-helm> kubectl port-forward service/pihole-jfwenisch-web 8088:80
Forwarding from 127.0.0.1:8088 -> 80
Forwarding from [::1]:8088 -> 80
```
This binds the port 80 from the service to 8088 on your localhost. Afterwards you are able to access the dashboard by opening you browser and navigating to 127.0.0.1:8088/admin/

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
  




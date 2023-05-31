# DnsMask

## Presentation & usefull links

https://www.linuxtricks.fr/wiki/dnsmasq-le-serveur-dns-et-dhcp-facile-sous-linux

## Install

Dnsmak allows a domain and all subdomain to an IP.  
If my case, all `*.web.local` will point to the raspberry-pi server, thus allowing me to not edit the `hosts` file on each device of the local network.  

let's install dnsMask  
`sudo apt install dnsmasq`  

Update `/etc/dnsmasq.conf` file  
Find the line address and listen address, uncomment and update   
```
address=/web.local/192.168.0.26  
listen-address=192.168.0.26
resolv-file=/etc/dnsmasq-resolv.conf
```  

I created a dnsmasq-resolv.conf file with :
```
nameserver 192.168.0.26
nameserver 8.8.8.8
```

Restart the service   
`sudo systemctl restart dnsmasq`  

## Set up other devices

On your router 192.168.0.1, update the dns to your raspberry ip 

You probably will need to update the dns on each of your devices. What I did on windows
Edit the dns on my ethernet configuration.
Flush the dns : `ipconfig /flushdns`

## Troubleshooting

You can try to ping the raspberry from a different device vie the ip or domain (web.local)   
You can also test a `nslookup web.local`

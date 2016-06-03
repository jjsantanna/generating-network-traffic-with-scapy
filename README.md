# spoofingwithscapy
Some python scripts to spoof requests to services (DNS, UPnP)

## Requirements
```bash
sudo apt-get -y install python-pip
sudo pip install scapy
sudo python
```

## (Spoofed) DNS request

```python
from scapy.all import *
dnsQuery="ddosdb.org"
spoofedIPsrc="130.89.14.206"
dnsResolver1="8.8.8.8"
dnsResolver2="130.89.0.128"

spoofedDNSRequest = IP(src=spoofedIPsrc,dst=dnsResolver1)/UDP()/DNS(rd=1,qd=DNSQR(qname=dnsQuery))

sr1(spoofedDNSRequest)
````

## (Spoofed) SSDP request to discover UPnP devices

```python
from scapy.all import *
spoofedIPsrc="130.89.14.206"

payload = "M-SEARCH * HTTP/1.1\r\n" \ #It is a HTTP over UDP or HTTPU
"HOST:239.255.255.250:1900\r\n" \     #It is always the multicast IP address 239.255.255.250
"ST:upnp:rootdevice\r\n" \            #Search Target
"MAN: \"ssdp:discover\"\r\n" \      
"MX:4\r\n\r\n"                        #Maximum waiting time (should be in the interval [1,5[)

ssdpRequest = IP(src=spoofedIPsrc,dst="239.255.255.250") / UDP(sport=1900, dport= 1900) / payload
sr1(ssdpRequest)
```

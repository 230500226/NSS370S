# Lab7

## Static R1
```plaintext
R1> enable

R1# configure terminal
R1(config)# ip route 192.168.2.0 255.255.255.0 10.1.1.6 150
R1(config)# ip route 192.168.3.0 255.255.255.0 10.1.1.6 150
R1(config)# ip route 10.1.1.8 255.255.255.252 10.1.1.6 150
R1(config)# end

R1# copy running-config startup-config

## Static R2

R2> enable

R2# configure terminal
R2(config)# ip route 192.168.1.0 255.255.255.0 10.10.10.5 150
R2(config)# ip route 192.168.3.0 255.255.255.0 10.10.10.5 150
R2(config)# end

R2# copy running-config startup-config
Destination filename [startup-config]?
Building configuration...
[OK]

  

R2#copy running-config startup-config 

Destination filename [startup-config]?  

Building configuration... 

[OK] 

## Static R3

R3> enable

R3# configure terminal
R3(config)# ip route 192.168.2.0 255.255.255.0 10.1.1.9 150
R3(config)# ip route 192.168.1.0 255.255.255.0 10.1.1.9 150
R3(config)# end

R3# copy running-config startup-config

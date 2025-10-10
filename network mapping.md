Here’s a clear list of VyOS and Ubuntu (ODL) interface configuration commands for your 6-router topology, **matching the cable connections you provided**.

---

## **1. Interface-to-IP Mapping**

Let's assign IPs using private /30 subnets for point-to-point links.  
**Routers:** vy1, vy2, vy3, vy4, vy5, vy6  
**Controller:** ODL (Ubuntu)

| Link                    | Interface 1      | Interface 2      | Subnet         | IP 1         | IP 2         |
|-------------------------|------------------|------------------|----------------|--------------|--------------|
| ODL – vy1               | ODL e0           | vy1 e0           | 10.0.0.0/30    | 10.0.0.1     | 10.0.0.2     |
| vy1 – vy2               | vy1 e1           | vy2 e0           | 10.0.0.4/30    | 10.0.0.5     | 10.0.0.6     |
| vy1 – vy6               | vy1 e2           | vy6 e1           | 10.0.0.8/30    | 10.0.0.9     | 10.0.0.10    |
| vy2 – vy3               | vy2 e1           | vy3 e0           | 10.0.0.12/30   | 10.0.0.13    | 10.0.0.14    |
| vy3 – vy4               | vy3 e1           | vy4 e0           | 10.0.0.16/30   | 10.0.0.17    | 10.0.0.18    |
| vy4 – vy5               | vy4 e1           | vy5 e0           | 10.0.0.20/30   | 10.0.0.21    | 10.0.0.22    |
| vy5 – vy6               | vy5 e1           | vy6 e0           | 10.0.0.24/30   | 10.0.0.25    | 10.0.0.26    |

---

## **2. VyOS Configuration Commands**

!NB Use show interface to get correct `ethN` value

### **vy1**
``` bash
configure
set interfaces ethernet eth0 address 10.0.0.2/30     # To ODL
set interfaces ethernet eth1 address 10.0.0.5/30     # To vy2
set interfaces ethernet eth2 address 10.0.0.9/30     # To vy6
set protocols ospf area 0 network 10.0.0.0/30
set protocols ospf area 0 network 10.0.0.4/30
set protocols ospf area 0 network 10.0.0.8/30
commit
save
```

### **vy2**
``` bash
configure
set interfaces ethernet eth0 address 10.0.0.6/30     # To vy1
set interfaces ethernet eth1 address 10.0.0.13/30    # To vy3
set protocols ospf area 0 network 10.0.0.4/30
set protocols ospf area 0 network 10.0.0.12/30
commit
save
```

### **vy3**
```bash
configure
set interfaces ethernet eth5 address 10.0.0.14/30    # To vy2
set interfaces ethernet eth6 address 10.0.0.17/30    # To vy4
set protocols ospf area 0 network 10.0.0.12/30
set protocols ospf area 0 network 10.0.0.16/30
commit
save
```

### **vy4**
```bash
configure
set interfaces ethernet eth0 address 10.0.0.18/30    # To vy3
set interfaces ethernet eth1 address 10.0.0.21/30    # To vy5
set protocols ospf area 0 network 10.0.0.16/30
set protocols ospf area 0 network 10.0.0.20/30
commit
save
```

### **vy5**
```bash
configure
set interfaces ethernet eth0 address 10.0.0.22/30    # To vy4
set interfaces ethernet eth1 address 10.0.0.25/30    # To vy6
set protocols ospf area 0 network 10.0.0.20/30
set protocols ospf area 0 network 10.0.0.24/30
commit
save
```

### **vy6**
```bash
configure
set interfaces ethernet eth0 address 10.0.0.26/30    # To vy5
set interfaces ethernet eth1 address 10.0.0.10/30    # To vy1
set protocols ospf area 0 network 10.0.0.24/30
set protocols ospf area 0 network 10.0.0.8/30
commit
save
```

---

## **3. Ubuntu (ODL Controller) Interface**

Assuming ODL runs on Ubuntu and you want to set the IP on eth0:
```bash
sudo ip addr add 10.0.0.1/30 dev eth0
sudo ip link set eth0 up
```
Or edit `/etc/netplan/01-netcfg.yaml` for permanent config (Ubuntu 18.04+):

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 10.0.0.1/30
```
Then apply:
```bash
sudo netplan apply
```

---

## **4. OSPF Summary**

- **Each VyOS router** advertises all its connected subnets into OSPF area 0.
- **ODL** does not run OSPF, but communicates with vy1 via BGP (see project steps).

---

## **5. Optional: Check and Clear Existing Configs (VyOS)**

Before configuring, you may want to clear old settings:
```bash
show configuration
configure
delete protocols ospf
delete protocols bgp
delete interfaces ethernet
commit
save
```

---

**Let me know if you want full configs (with BGP for ODL, etc.) or interface naming tweaks!**

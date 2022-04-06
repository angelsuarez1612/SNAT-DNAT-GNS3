# SNAT-DNAT-GNS3
## Configuración Cisco.
-------------------------------------------
### Configuración tarjeta de red router.

- conf t
- int Interfaz
- ip addr IP MASK
- no sh


### Configuración tarjeta de red VPCS.

- ip dhcp
- ip IP/MASK GATEWAY


## Configuración Linux.
-------------------------------------------
### Configuración SNAT LINUX

#### SNAT

      SNAT --> up iptables -t nat -A POSTROUTING -s (red privada) -o (interfaz ip publica) -j SNAT --to (ip publica)
         down iptables -t nat -A POSTROUTING -s (red privada) -o (interfaz ip publica) -j SNAT --to (ip publica)

#### DNAT
      DNAT --> up iptables -t nat -A PREROUTING -p tcp --dport 80 -i eth* -j DNAT --to (ip privada)
         down iptables -t nat -A PREROUTING -p tcp --dport 80 -i eth* -j DNAT --to (ip privada)

      ssh --> up iptables -t nat -A PREROUTING -p ssh --dport 22 -i (interfaz ip publica) -j DNAT --to (ip privada)
              down iptables -t nat -A PREROUTING -p ssh --dport 22 -i (interfaz ip publica) -j DNAT --to (ip privada)
              

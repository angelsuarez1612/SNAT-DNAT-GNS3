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
### Crear server dhcp en toolbox:
- Instalamos isc-dhcp-server
- Damos ips a nuestras interfaces
- Accedemos al fichero /etc/default/isc-dhcp-server y asignamos la interfaz que va a dar las ipv4
- Configuramos los parámetros que van a obtener los dispositivos por dhcp en el fichero /etc/dhcp/dhcpd.conf

**ejemplo**

	subnet IPprivada netmask (netmask) {
		range IP1 IP2;
		option domain-name-server (ip interfaz privada);
		option routers (ip interfaz privada);
		option broadcast-address (ip interfaz privada);
	}     
	
- Reiniciamos el servicio con service isc-dhcp-server restart

### ACLs
## Básicas
Sintaxis:
access-list número {permit o deny} dirección máscara
- Denegar o permitir todo un tráfico:
access-list 1 {deny | permit} any
- Denegar o permitir de una dirección específica:
access-list 1 {deny | permit} host dirección

## Extendidas
Verifican otros muchos elementos más que las básicas como por ejemplo protocolos, números de puerto… (los puertos se ponen como lt - menor que, gt - mayor que, eq - igual que, neq - distinto que)
- Sintaxis:
access-list número(a partir del 100 hasta la 199) {permit | deny} protocolo ip_origen máscara_origen ip_destino máscara_destino operador(eq, lt, gt…) puerto


*Asignar ACLs a su interfaz correspondiente con el comando:
ip access-group numero_acl out/in

-------------------------------------------
### Configuración SNAT LINUX

#### SNAT

	up iptables -t nat -A POSTROUTING -s (red privada) -o (interfaz ip publica) -j SNAT --to (ip publica)
        down iptables -t nat -A POSTROUTING -s (red privada) -o (interfaz ip publica) -j SNAT --to (ip publica)

#### DNAT

	up iptables -t nat -A PREROUTING -p tcp --dport 80 -i eth* -j DNAT --to (ip privada)
	down iptables -t nat -A PREROUTING -p tcp --dport 80 -i eth* -j DNAT --to (ip privada)
	
**ssh**

	up iptables -t nat -A PREROUTING -p ssh --dport 22 -i (interfaz ip publica) -j DNAT --to (ip privada)
	down iptables -t nat -A PREROUTING -p ssh --dport 22 -i (interfaz ip publica) -j DNAT --to (ip privada)
              

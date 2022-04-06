# · Apuntes GNS3 ·  

## Configuración Cisco.
-------------------------------------------
### Configuración tarjeta de red router.

- `conf t`
- `int Interfaz`
- `ip addr IP MASK`
- `no sh`


### Configuración tarjeta de red VPCS.

- `ip dhcp`
- `ip IP/MASK GATEWAY`

### Enrrutamiento

- `conf t`
- `ip route IP MASK IP_DESTINO`


<br>

## Configuración Linux.
-------------------------------------------
### DHCP SERVER Toolbox:
_Nota: Primero tenemos que configurar las interfaces._
- `nano /etc/default/isc-dhcp-server` y añadimos la interfaz que repartirá en `INTERFACESv4=""`
- Añadimos ámbito DHCP(parametros que dará) `nano /etc/dhcp/dhcpd.conf`

**Ejemplo**

	subnet RED netmask MASK {
		range IP1 IP2;
		option routers IP_GATEWAY;
		option broadcast-address x.x.x.255;
	}     
	
- Iniciamos el sercicio con `/etc/init.d/isc-dhcp-server start`

**Errores DHCP**<br>
Cuando reiniciamos el ToolBox en el que está instaldo, el servicio queda parado, si lo intentamos arrancar de nuevo nos puede dar un error del estilo:
`*dhcpd service already running (pid file /var/run/dhcpd.pid currenty exists)`.<br>

Para poder solucionarlo tenemos que eliminar el dhcpd.pid con `rm /var/run/dhcpd.pid`.<br>

Y nuevamente iniciamos el servicio con `/etc/init.d/isc-dhcp-server start`.

<br>

### Enrrutamiento Linux
- `ip route add IP/MASK via IP_DESTINO`
<br>

## ACLs
-------------------------------------------
### Básicas
Sintaxis:
access-list número {permit o deny} dirección máscara
- Denegar o permitir todo un tráfico:
`access-list 1 {deny | permit} any`
- Denegar o permitir de una dirección específica:
`access-list 1 {deny | permit} host dirección`

### Extendidas
Verifican otros muchos elementos más que las básicas como por ejemplo protocolos, números de puerto… (los puertos se ponen como lt - menor que, gt - mayor que, eq - igual que, neq - distinto que)
- Sintaxis:
`access-list número(a partir del 100 hasta la 199) {permit | deny} protocolo ip_origen máscara_origen ip_destino máscara_destino operador(eq, lt, gt…) puerto`

*Asignar ACLs a su interfaz correspondiente con el comando:
`ip access-group numero_acl out/in`
<br>

### Configuración SNAT

#### SNAT

	up iptables -t nat -A POSTROUTING -s (red privada) -o (interfaz ip publica) -j SNAT --to (ip publica)
	down iptables -t nat -D POSTROUTING -s (red privada) -o (interfaz ip publica) -j SNAT --to (ip publica)

### Configuración DNAT

#### DNAT

	up iptables -t nat -A PREROUTING -p tcp --dport 80 -i (interfaz ip publica) -j DNAT --to (ip privada)
	down iptables -t nat -D PREROUTING -p tcp --dport 80 -i (interfaz ip publica) -j DNAT --to (ip privada)
	
#### ssh

	up iptables -t nat -A PREROUTING -p ssh --dport 22 -i (interfaz ip publica) -j DNAT --to (ip privada)
	down iptables -t nat -D PREROUTING -p ssh --dport 22 -i (interfaz ip publica) -j DNAT --to (ip privada)
	
<br>


## Firewall














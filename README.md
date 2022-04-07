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
- Descomentar linea `net.ipv4.ip_forward=1` en el fichero `/etc/sysctl.conf`
<br>

## ACLs
-------------------------------------------
### Básicas
Sintaxis:
`access-list número {permit o deny} direccion máscara`
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

**Añadir regla:**(Se añade al final de todas las reglas)<br>

`iptables -A [CADENA] [-p PROTOCOLO] [-s IP_ORIGEN] [-d IP_DESTINO] [-i INTERFAZ_ENTRADA] [-o INTERFAZ_SALIDA] [-j ACCEPT|DROP]`<br>

**Eliminar regla:**<br>

`iptables -D [CADENA] [-p PROTOCOLO] [-s IP_ORIGEN] [-d IP_DESTINO] [-i INTERFAZ_ENTRADA] [-o INTERFAZ_SALIDA] [-j ACCEPT|DROP]`<br>

**Listar reglas:**<br>

`iptables [-t tabla ] -L [CADENA] [-n] [-v] [--line-numbers]` o `iptables [-t tabla] -S [CADENA] [-v]`

**Política por defecto:**<br>

`iptables [-t tabla] -P CADENA ACCEPT|DROP`
_Nota: El target se ejecutará cuando los paquetes no cumplan ninguna regla de la chain especificada. _

La tabla filter cuenta con 3 chains o cadenas predefinidas:

### INPUT
Paquetes dirigidos al cortafuegos.

**Sintaxis:**<br>

`iptables -A INPUT [-p PROTOCOLO] [-s IP_ORIGEN] [-d IP_DESTINO] [-i INTERFAZ_ENTRADA] [-j ACCEPT|DROP]`

### OUTPUT
Paquetes generados localmente.

**Sintaxis:**<br>
`iptables -A INPUT [-p PROTOCOLO] [-s IP_ORIGEN] [-d IP_DESTINO] [-o INTERFAZ_SALIDA] [-j ACCEPT|DROP]`

### FORWARD
Paquetes errutados a través de la máquina para otras redes.

**Ejemplo:**<br>
Para permitir protocolo ICMP(ping)<br>
`iptables -A FORWARD -s IP_ORIGEN -m icmp --icmp-type echo-request -j ACCEPT`


### SSH

1. Añadir NAT en Tiny Core y ToolBox.
2. Instalar en ambos ssh
- Tiny Core: `tce-load -wi openssh`
- Linux: `apt update && apt install ssh`
3. Editamos el fichero con `nano /etc/ssh/sshd_config`(Esto ya solo en el ToolBox)
4. Descomentar las lineas:
- `Port 22`
- `PermitRootLogin` _poner_ `yes`
- `passwordAuthentication yes` 	
5. Reiniciamos el servicio con `service ssh restart`



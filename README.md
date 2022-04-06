# SNAT-DNAT-GNS3

### toolbox ###

      SNAT --> up iptables -t nat -A POSTROUTING -s (red privada) -o (interfaz ip publiga) -j SNAT --to (ip publica)
         down iptables -t nat -A POSTROUTING -s (red privada) -o (interfaz ip publiga) -j SNAT --to (ip publica)
         
      DNAT --> up iptables -t nat -A PREROUTING -p tcp --dport 80 -i eth* -j DNAT --to (ip privada)
         down iptables -t nat -A PREROUTING -p tcp --dport 80 -i eth* -j DNAT --to (ip privada)

      ssh --> up iptables -t nat -A PREROUTING -p ssh --dport 22 -i eth* -j DNAT --to (ip privada)
              down iptables -t nat -A PREROUTING -p ssh --dport 22 -i eth* -j DNAT --to (ip privada)
              
### cisco ###

     SNAT --> access-list 1 permit ip wilcardmask
              ip nat pool ip_publica IP1 IP2 netmask mascara
              ip nat inside source list NUMACL pool ip_publica overload
              ip nat inside --> interfaz interna
              ip nat outside -- interfaz externa

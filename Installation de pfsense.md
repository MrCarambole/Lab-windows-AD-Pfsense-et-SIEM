J'ai suivie ce tutoriel : https://www.youtube.com/watch?v=NzVDjNqchoc

### Installation de pfsense
### Configuration des interfaces

J'ai du inverser les deux interfaces.

WAN -> em1 DHCPv4 192.168.1.42  (donner par ma box)
LAN -> em0 10.10.10.1/24

Je n'ai pas activé le DHCP car c'est l'AD qui aura ce role.

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/interface%20admin%20de%20pfsense.PNG">

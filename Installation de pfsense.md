J'ai suivie ce tutoriel : https://www.youtube.com/watch?v=NzVDjNqchoc

### Installation de pfsense
### Configuration des interfaces

J'ai du inverser les deux interfaces.

WAN -> em1 DHCPv4 192.168.1.42  (donner par ma box)
LAN -> em0 10.10.10.1/24

Je n'ai pas activé le DHCP car c'est l'AD qui aura ce role.

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/interface%20admin%20de%20pfsense.PNG">

### Mettre le serveur windows dans le réseau interne
Pour configurer le pare feu je dois utiliser une machine à l'intérieur du réseau interne (il est logique de ne pas pouvoir administrer depuis le WAN, bien que certain réglage temporaire puissent le permettre).

Je vais donc utiliser ma machine windows serveur (j'espère que les deux vont pouvoir ce lancer en meme temps).

J'ai du configurer mon serveur windows pour qu'il soit dans le réseau internet "intnet"

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/windows%20serveur%20config%20r%C3%A9seau.PNG">

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/config%20reseau%20windows.PNG">

Et ça fonctionne ! :)

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/sa%20marche.PNG">

### Configuration du pare feu depuis le réseau interne


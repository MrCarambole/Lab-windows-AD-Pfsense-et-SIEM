# Lab Windows serveur, Pare-feu Pfsense, IDS Snort et Siem Wazuh

## Cours suivi lors de ce lab :
#### Windows Serveur:
https://openclassrooms.com/fr/courses/2356306-prenez-en-main-windows-server/5832816-apprehendez-windows-server

#### Pfsense :
https://www.youtube.com/watch?v=NzVDjNqchoc

#### Snort :

#### Wazuh :

## Installation d'un serveur Windows
### Nomenclature windows serveur

Première version de windows serveur : windows NT

puis Windows server 2003, 2008, 2012 etc...

Plusieurs éditions de Win serv : 

Essentials (jusqu'a 25 utilisateurs)

Standard (pour les entreprises n'ayant pas de fort besoin en virtualisation)

Datacenter (permet de crée autant de machines virtuelles que l'on souhaite)


### Création de la vm

iso : https://www.microsoft.com/fr-fr/evalcenter/download-windows-server-2019

4096 de mémoire vive

2 cpu

et ça reste très lent...

### Post installation
Installation des mises à jour [OK]

Renommage de la machine [OK]

*Pour pouvoir continuer notre configuration de serveur windows on va devoir s'occuper du réseau, mais cela nécessite d'abord de mettre en place de le réseau interne via le parfeu.*

## Installation et configuration de pfsense
Pour l'installation de pfsense rien de très compliqué, il suffit de suivre le menu d'installation. J'ai cependant eu un problème car il avait inversé mes deux interface, il à donc fallut les changers.
A savoir qu'un parfeu à deux interface : La WAN et la LAN
La LAN c'est le réseau interne protégé par le parfeu et la WAN désigne le réseau extérieur.

WAN -> em1 DHCPv4 192.168.1.42  (donner par ma box)
LAN -> em0 10.10.10.1/24

Lors de ma config pfsense je n'ai pas activé le DHCP car c'est l'AD qui aura ce role.

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/interface%20admin%20de%20pfsense.PNG">

Pour utilisé l'interface web de configuration de pfsense je suis passé par une machine debian desktop.

Voici les régles de base sur le parfeu :
<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/config%20pfsense%201.PNG">

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/config%20pfsense%202.PNG">

Autoriser toute communication initié par le LAN vers le WAN. Mais interdit toute connexion à l'origine du WAN vers le LAN.
Ce configuration nous suffit pour l'instant, on rajoutera d'autre régles plus tard pour le SIEM.


## Mettre le serveur windows dans le réseau interne
<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/windows%20serveur%20config%20r%C3%A9seau.PNG">

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/config%20reseau%20windows.PNG">

Ainsi windows est dans le réseau interne et peu toujours avoir accès a internet car il utilise pfsense comme paserel qui laisse le flux aller jusqu'a ma livebox.

## Mise en place des roles du serveur windows :
Il existe tout un tas de role que peu avoir un serveur windows. Dans mon cas je veux que mon serveur est les roles suivant :
- Un DHCP (les machines du réseau pourront automatiquement recevoir une ip de la part de ce serveur
- Active directory (implémentation par microsoft des annuaire ldap permettant la gestion des identité et des droits d'accès)
- Un serveur web iis (accesible depuis le WAN, il faudra donc configurer le pfsense pour le permettre)
- Un service de stockage (également accesible depuis le WAN)

### Mise en place du DHCP :
Pour cela on rajout le role dhcp dans l'interface de gestion serveur.
On crée une nouvelle étendu:
- ip debut : 10.10.10.10
- ip fin : 10.10.10.50
- passerel : 10.10.10.1

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/dhcp.PNG">

Je test avec une machine dans le réseau interne :
<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/dhcp%202.PNG">

et ça fonctionne !

### Mise en place du serveur web :
Pour installer le serveur IIS il faut juste rajouter le role au serveur windows. Jusque là rien de bien compliqué :
<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/srv_iis.PNG">

Afin de pouvoir acceder au site hébergé par IIS il faut effectuer un routage NAT de type port forwarding.

Le routage NAT à pris une semaine à etre debugé... mais il fonctionne ! (le problème c'est que j'avais oublié de mettre la regle "block private networks" qui passé en priorité par rapport à l'autorisation du port 80).
<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/proof%20routage.PNG">

### Mise en place du service de stockage :
Concernant le stockage le plus compliqué fut de crée un nouveau disque et de l'initialiser. J'ai également perdu beaucoup de temps car je n'avais pas activer une fonctionnalité nécessaire au partage, croyant que le service de fichier et de stockage était suffisant. Il faut enfaite rajouter la fonctionnalité : nom pour activer smb

<img src="https://raw.githubusercontent.com/MrCarambole/Lab-windows-AD-Pfsense-et-SIEM/main/partage.PNG">

Expliquer ce qu'est SMB

### Mise en place du service de l'active directory :

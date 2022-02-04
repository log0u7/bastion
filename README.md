# Bastion
```
                                  |>>>
                                  |
                    |>>>      _  _|_  _         |>>>
                    |        |;| |;| |;|        |
                _  _|_  _    \\.    .  /    _  _|_  _
               |;|_|;|_|;|    \\:. ,  /    |;|_|;|_|;|
               \\..      /    ||;   . |    \\.    .  /
                \\.  ,  /     ||:  .  |     \\:  .  /
                 ||:   |_   _ ||_ . _ | _   _||:   |
                 ||:  .|||_|;|_|;|_|;|_|;|_|;||:.  |
                 ||:   ||.    .     .      . ||:  .|
                 ||: . || .     . .   .  ,   ||:   |       \,/
                 ||:   ||:  ,  _______   .   ||: , |            /`\
                 ||:   || .   /+++++++\    . ||:   |
                 ||:   ||.    |+++++++| .    ||: . |
              __ ||: . ||: ,  |+++++++|.  . _||_   |
     ____--`~    '--~~__|.    |+++++__|----~    ~`---,              ___
-~--~                   ~---__|,--~'                  ~~----_____-~'   `~----~~
```

## Qu'est-ce qu'un bastion ? 
Le bastion n'est pas un simple proxy/relay/gateway pour les connexions SSH, 
c'est un élément clef de la sécurité périmétrique de l'entreprise qui réponds à des problématiques variés :  

- Gestion des connexions extérierieur à l'entreprise vers les infrastructures : sous-traitants, collaborateurs externe.
- Gestion des connexions interne à l'entreprise vers les infrastructures : colaborateurs interne, utilisateurs d'un VPN commun et/ou dédié à l'administration.
- Gestion des droits d'accès : utilisateurs, groupes et périmètre d'action.
- Gestion de la légitimité et tracabilité d'une connexion externe ou interne à l'entreprise.

A l'instar d'un proxy-cache authentifiant (Squid + auth LDAP/AD pour les users, auth basic pour les serveurs) pour les connexions vers l'exterieur, 
ou bien d'un reverse-proxy (Traefik/HAProxy) pour les connexions entrantes vers nos services. 
Le bastion est le mandataire de confiance, le point d'entrée unique pour l'administration des infrastructures offrant visibilité et tracabilité sur les evénements qui y occurent.

## Pour quel usage ?
Afin de suivre les recomandations de la majorité des organismes produisant des référentiels sur la sécurité des systèmes d'information. 

La mise en conformité avec RGPD, ISO27001, PCI-DSS demande aux organisations de metre en place (entre autre) : 
- Délégation de l'administration et politique de moindre privilèges.
- Contrôle des accès basé sur les différents rôles des intervenants.
- Chiffrement obligatoire des connexions à des interfaces d'administration.
- Traçabilité et enregistrements automatique des connexions.
- Analyse des flux SSH et des sessions intéractives.

Aussi le bastion d'administration reste une solution de sécurité pérène pour répondre à ces besoins.

## Définition du besoin

### Fonctionnalités éssentielles :  
- Point d'entré unique
- Haute Disponibilité
- Adaptabilité
- Compatibilité
- Tracabilité
- Auditabilité
- Gestion des accès centralisé
- Délégation
- Verouillage des sessions inactives
- Enregistrement des sessions interactive
- Enregistrement des sessions non-interactive

### Fonctionnalités optionnelles (Non Exhaustif) :
- Support RBAC
- Support SSHFP
- Support SCP/SFTP
- Support MOSH
- Support SuDo
- Support Ansible
- Support PortKnocking/SPA
- Support LDAP/AD
- Support Proxy HTTPS
- Support SIEM
- Support PKI 
- Support MFA/OTP
- Support Push
- Support PIV
- Support HSM
- Support Geolocalisation
- Support Inter-entreprise (multiple trust/interco)

## Comparatif des solutions

### SpanKey¹ (RCDevs) :
[SpanKey](https://www.rcdevs.com/products/spankey/) n'est pas un bastion mais un service client/serveur de gestion des clefs SSH centralisé.

- [ ] Point d'entré unique²
- [X] Haute Disponibilité
- [X] Adaptabilité
- [ ] Compatibilité
- [X] Tracabilité
- [X] Auditabilité
- [X] Gestion des accès 
- [X] Délégation
- [X] Verouillage des sessions inactives
- [X] Enregistrement des sessions interactive
- [ ] Enregistrement des sessions non-interactive²
--- 
- [ ] Support RBAC²
- [ ] Support SSHFP²
- [X] Support SCP/SFTP
- [ ] Support MOSH²
- [X] Support SuDo
- [ ] Support Ansible²
- [ ] Support PortKnocking/SPA²
- [X] Support LDAP/AD
- [ ] Support Proxy HTTPS²
- [X] Support SIEM
- [ ] Support PKI² 
- [X] Support MFA/OTP
- [X] Support Push
- [X] Support PIV
- [X] Support HSM
- [X] Support Geolocalisation
- [ ] Support Inter-entreprise (multiple trust/interco)²

_¹ Version payante_

_² Non définis ou non applicable_

#### Pros : 
- Intégration native avec les autres produits RCDev.
- Support technique auprès de l'éditeur.

#### Cons :
- Prix : €3,30 par agent par mois pour 100 serveurs (en plus des ressources alloués).
- Sources fermés : Auditabilité du code serveur et agent impossible.
- Compatibilité réduite : Agent seulement compatible avec les distribution Linux basées RedHat ou Debian.
- Architecture Client/Serveurs : Augmentation de la surface d'attaques des hôtes via l'installation d'un agent.

#### Références : 
1. [Documentation](https://www.rcdevs.com/docs/)
2. [Spankey Solution](https://www.rcdevs.com/products/spankey/#1562770968312-4fda5b9e-69b5)
3. [Spankey Features](https://www.rcdevs.com/products/spankey/#1551685627571-e7270aee-8361155168998901715516901220271562599540024)
4. [Spankey Architecture](https://www.rcdevs.com/products/spankey/#1551685627585-705199c1-7e34155168998901715516901220271562599540024)
5. [Spankey HowTo](https://www.rcdevs.com/products/spankey/#1551685627585-705199c1-7e34155168998901715516901220271562599540024)

### TheBastion (OVH) :
[TheBastion](https://github.com/ovh/the-bastion) est un bastion OpenSSH devellopé en "interne" par OVH puis rendus publique (Q4 2020).

- [X] Point d'entré unique
- [X] Haute Disponibilité
- [X] Adaptabilité
- [X] Compatibilité
- [X] Tracabilité
- [X] Auditabilité
- [X] Gestion des accès
- [X] Délégation
- [X] Verouillage des sessions inactives
- [X] Enregistrement des sessions intéractives
- [X] Enregistrement des sessions non-intéractives
---
- [X] Support RBAC²
- [X] Support SSHFP
- [X] Support SCP/SFTP
- [X] Support MOSH
- [X] Support SuDo
- [X] Support Ansible
- [X] Support PortKnocking/SPA
- [ ] Support LDAP/AD¹
- [X] Support Proxy HTTPS
- [X] Support SIEM
- [X] Support PKI 
- [X] Support MFA/OTP
- [ ] Support Push¹
- [X] Support PIV
- [ ] Support HSM¹
- [ ] Support Geolocalisation¹
- [X] Support Inter-entreprise (multiple trust/interco)

_¹ Un greffons peu eventuellement rajouter cette fonctionnalité_

_² Le DAC avec groupes (tel qu'implémenté dans les systèmes de fichiers POSIX) se rapproche au niveau fonctionnel d'un RBAC._
#### Pros :
- Prix : Gratuit (en dehors des ressources alloués)
- Source ouverte : Auditable et extensible.
- Compatibilité : le serveur s'installe sur n'importe quel systemes BSD/Linux (OpenSSH + Perl) et sécurise n'importe quel hôte faisant tourner un serveur ssh ou telnet.
- Durcissment : La solution est basée sur le durcissement de l'OS conforme au PCI-DSS ([debian-CIS](https://github.com/ovh/debian-cis)) et propose des template pour le durcissement du serveur SSH.
- Fiabilité : Ce logiciel est exploité en production depuis plusieurs annéés dans de nombreux environements certifiés  PCI-DSS, ISO27001, SOC 1&2. 

#### Cons :
- Absence de support technique de l'éditeur.
- Pas d'intégration LDAP/AD native.

#### Références :
1. [Documentation](https://ovh.github.io/the-bastion/index.html)
2. [Article Bastion part 1 : Genesis](https://blog.ovhcloud.com/the-ovhcloud-bastion-part-1/)
3. [Article Bastion part 2 : Delegation dizziness](https://blog.ovhcloud.com/the-ovhcloud-ssh-bastion-part-2-delegation-dizziness/)
4. [Article Bastion part 3 : Security at the core](https://blog.ovhcloud.com/the-bastion-part-3-security-at-the-core/)
5. [Article Bastion part 4 : A new era](https://blog.ovhcloud.com/the-bastion-part-4-a-new-era/)

#### PoC

Ce PoC est basé sur l'image docker publique [ovhcom/the-bastion:sandbox](https://hub.docker.com/r/ovhcom/the-bastion),
Cette image n'as pas pour vocation d'être utiliser en production, il est préférable de se baser sur les fichiers [Dockerfile](https://github.com/ovh/the-bastion/tree/master/docker) fournis par le projets et de les adapter au besoins.

Création des réseaux :
```sh
docker network create bastion --scope swarm --driver overlay --internal
docker network create net1 --scope swarm --driver overlay --internal
docker network create net2 --scope swarm --driver overlay --internal
docker network create net3 --scope swarm --driver overlay --internal
docker network ls
```
Déploiement de la stack bastion :
```sh
docker stack deploy -c bastion/docker-compose.yml bastion
docker stack ls
docker service ls
```
Création du compte d'administration sur le master :
_Notes : Dans cet exemple mon fichier `~/.ssh/config` est déjà parametré pour utiliser la clefs privé qui vas avec la clefs publique à fournir au script `setup-first-admin-account.sh`._
```sh
docker exec -it $(docker ps |grep bastion_master |awk '{print $1}') /opt/bastion/bin/admin/setup-first-admin-account.sh admin auto
alias bastion="ssh admin@127.0.0.1 -tp 2222 -- "
bastion --osh info
```
Listing de la clefs publique de sortie  pour l'utilisateur admin dans la configuration des instances de la stack server :
```sh
bastion --osh selfListEgressKeys
```
Copie de la directive authorized_keys dans le fichier `server\admin`
Deploiement de la stack server:
```sh
docker stack deploy -c ssh/docker-compose.yml ssh
docker stack ls
docker service ls
```
Création des accès sur le bastion
```sh
for i in {1..3};do bastion --osh selfAddPersonalAccess --host $(docker inspect -f '{{ range.NetworkSettings.Networks }}{{.IPAddress}}{{end}}' $(docker ps |grep ssh_server$i |awk '{print $1}')) --port 22 --user admin; done
```
Vérification :
```sh
bastion --osh selfListAccesses
```
Connection à au serveur 1 :
```sh
bastion admin@$(docker inspect -f '{{ range.NetworkSettings.Networks }}{{.IPAddress}}{{end}}' $(docker ps |grep ssh_server1 |awk '{print $1}'))
```
Listing des sessions SSH enregitrée ou en cours avec détails
```sh
bastion --osh selfListSessions --type ssh --detailed
```
Rejouer la une session ssh :
```sh
bastion --osh selfPlaySession --id xxxxxxxxxxxx
```

Il est possible d'utiliser le bastion en mode interactif avec les commandes : 

`bastion` et `bastion -i` pour profiter de la completion automatique (et ne plus taper --osh).

Une aide détaillé est accèssible avec la commande `help`.

### Teleport (Gravitational)
[Teleport](https://github.com/gravitational/teleport) est un bastion modulaire moderne multiprotocoles.

#### Références : 
1. [Documentation](https://goteleport.com/docs/)
2. [How it work](https://goteleport.com/how-it-works/)
3. [Demo Video](https://www.youtube.com/watch?v=b1WHFW0NIoM)

# PoC TheBastion
```
                                o
                            .-'"|
                            |-'"|
                                |   _.-'`.
                               _|-"'_.-'|.`.
                              |:^.-'_.-'`.;.`.
                              | `.'.   ,-'_.-'|
                              |   + '-'.-'   J
           __.            .d88|    `.-'      |
      _.--'_..`.    .d88888888|     |       J'b.
   +:" ,--'_.|`.`.d88888888888|-.   |    _-.|888b.
   | \ \-'_.--'_.-+888888888+'  _>F F +:'   `88888bo.
    L \ +'_.--'   |88888+"'  _.' J J J  `.    +8888888b.
    |  `+'        |8+"'  _.-'    | | |    +    `+8888888._-'.
  .d8L  L         J  _.-'        | | |     `.    `+888+^'.-|.`.
 d888|  |         J-'            F F F       `.  _.-"_.-'_.+.`.`.
d88888L  L     _.  L            J J J          `|. +'_.-'    `_+ `;
888888J  |  +-'  \ L         _.-+.|.+.          F `.`.     .-'_.-"J
8888888|  L L\    \|     _.-'     '   `.       J    `.`.,-'.-"    |
8888888PL | | \    `._.-'               `.     |      `..-"      J.b
8888888 |  L L `.    \     _.-+.          `.   L+`.     |        F88b
8888888  L | |   \   _..--'_.-|.`.          >-'    `., J        |8888b
8888888  |  L L   +:" _.--'_.-'.`.`.    _.-'     .-' | |       JY88888b
8888888   L | |   J \ \_.-'     `.`.`.-'     _.-'   J J        F Y88888b
Y888888    \ L L   L \ `.      _.-'_.-+  _.-'       | |       |   Y88888b
`888888b    \| |   |  `. \ _.-'_.-'   |-'          J J       J     Y88888b
 Y888888     +'\   J    \ '_.-'       F    ,-T"\   | |    .-'      )888888
  Y88888b.      \   L    +'          J    /  | J  J J  .-'        .d888888
   Y888888b      \  |    |           |    F  '.|.-'+|-'         .d88888888
    Y888888b      \ J    |           F   J    -.              .od88888888P
     Y888888b      \ L   |          J    | .' ` \d8888888888888888888888P
      Y888888b      \|   |          |  .-'`.  `\ `.88888888888888888888P
       Y888888b.     J   |          F-'     \\ ` \ \88888888888888888P'
        Y8888888b     L  |         J       d8`.`\  \`.8888888888888P'
         Y8888888b    |  |        .+      d8888\  ` .'  `Y888888P'
         `88888888b   J  |     .-'     .od888888\.-'
          Y88888888b   \ |  .-'     d888888888P'
          `888888888b   \|-'       d888888888P
           `Y88888888b            d8888888P'
             Y88888888bo.      .od88888888   hs
             `8888888888888888888888888888
              Y88888888888888888888888888P
               `Y8888888888888888888888P'
                 `Y8888888888888P'
                      `Y88888P'
```
Ce PoC est basé sur l'image docker publique d'OVH.

Cette image n'as pas pour vocation d'être utiliser en production, 
il est préférable de se baser sur les fichiers [Dockerfile](https://github.com/ovh/the-bastion/tree/master/docker) fournis par le projets et de les adapter au besoins.

## Prérequis : 
- [Docker (swarm activé)](https://docs.docker.com/engine/swarm/swarm-mode/)
- [ovhcom/the-bastion:sandbox](https://hub.docker.com/r/ovhcom/the-bastion)
- [panubo/sshd:latest](https://hub.docker.com/r/panubo/sshd)

_Notes: Pour ce poc, le [dockerfile de la sandbox](https://github.com/ovh/the-bastion/blob/master/docker/Dockerfile.sandbox) à été modifié (correction des locales) :_
```diff
diff --git a/docker/Dockerfile.sandbox b/docker/Dockerfile.sandbox
index 1d1149f..3a68d6d 100644
--- a/docker/Dockerfile.sandbox
+++ b/docker/Dockerfile.sandbox
@@ -18,7 +18,9 @@ RUN \
     # cleanup packages cache to save space \
     rm -rf /var/cache/apt && \
     # handle locales \
-    echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && locale-gen && \
+    echo "en_US.UTF-8" >> /etc/locale.gen && locale-gen --purge en_US.UTF-8 && \
+    echo -e 'LANG="en_US.UTF-8"\nLANGUAGE="en_US.UTF-8"\nLC_ALL="C"\n' > /etc/default/locale && \
+    dpkg-reconfigure --frontend=noninteractive locales && \
     # disable /dev/kmsg handling by syslog-ng and explicitly enable /dev/log \
     sed -i -re 's=system\(\);=unix-stream("/dev/log");=' /etc/syslog-ng/syslog-ng.conf && \
     # accountUidMax & ttyrecGroupIdOffset change: fixes https://github.com/ovh/the-bastion/issues/24 \
```
_Il est donc nécéssaire d'éditer le fichier [bastion/docker-compose.yml](bastion/docker-compose.yml) pour changer l'image._

## Mise en oeuvre :
1. Création des réseaux :
```sh
docker network create bastion --scope swarm --driver overlay --internal
docker network create net1 --scope swarm --driver overlay --internal
docker network create net2 --scope swarm --driver overlay --internal
docker network create net3 --scope swarm --driver overlay --internal
docker network ls
```
2. Déploiement de la stack bastion :
```sh
docker stack deploy -c bastion/docker-compose.yml bastion
docker stack ls
docker service ls
```
3. Création du compte d'administration sur le master :
_Notes : Dans cet exemple mon fichier `~/.ssh/config` est déjà parametré pour utiliser la clefs privé qui vas avec la clefs publique à fournir au script `setup-first-admin-account.sh`._
```sh
docker exec -it $(docker ps |grep bastion_master |awk '{print $1}') /opt/bastion/bin/admin/setup-first-admin-account.sh admin auto
alias bastion="ssh admin@127.0.0.1 -tp 2222 -- "
bastion --osh info
```
4. Listing de la clefs publique de sortie  pour l'utilisateur admin dans la configuration des instances de la stack server :
```sh
bastion --osh selfListEgressKeys
```
5. Copie de la directive `authorized_keys` dans le fichier [ssh\admin](ssh/admin)
6. Deploiement de la stack server:
```sh
docker stack deploy -c ssh/docker-compose.yml ssh
docker stack ls
docker service ls
```
7. Création des accès sur le bastion
```sh
for i in {1..3};do \
  bastion --osh selfAddPersonalAccess --host $(docker inspect -f '{{ range.NetworkSettings.Networks }}{{.IPAddress}}{{end}}' \
  $(docker ps |grep ssh_server$i |awk '{print $1}')) --port 22 --user admin; \
done
```
8. Vérification :
```sh
bastion --osh selfListAccesses
```
9. Connection à au serveur 1 :
```sh
bastion admin@$(docker inspect -f '{{ range.NetworkSettings.Networks }}{{.IPAddress}}{{end}}' $(docker ps |grep ssh_server1 |awk '{print $1}'))
```
10. Listing des sessions SSH enregitrée ou en cours avec détails
```sh
bastion --osh selfListSessions --type ssh --detailed
```
11. Rejouer la une session ssh :
```sh
bastion --osh selfPlaySession --id xxxxxxxxxxxx
```

Il est possible d'utiliser le bastion en mode interactif avec les commandes : 

`bastion` et `bastion -i` pour profiter de la completion automatique (et ne plus taper --osh).

Une aide détaillé est accèssible avec la commande `help`.


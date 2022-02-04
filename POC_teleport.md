# PoC Teleport
```
                                           .,-:;//;:=,
                                     . :H@@@MM@M#H/.,+%;,
                                  ,/X+ +M@@M@MM%=,-%HMMM@X/,
                                 -+@MM; $M@@MH+-,;XMMMM@MMMM@+-
                                ;@M@@M- XM@X;. -+XXXXXHHH@M@M#@/.
                              ,%MM@@MH ,@%=            .---=-=:=,.
                              -@#@@@MX .,              -%HX$$%%%+;
                             =-./@M@M$                  .;@MMMM@MM:
                             X@/ -$MM/                    .+MM@@@M$
                            ,@M@H: :@:                    . -X#@@@@-
                            ,@@@MMX, .                    /H- ;@M@M=
                            .H@@@@M@+,                    %MM+..%#$.
                             /MMMM@MMH/.                  XM@MH; -;
                              /%+%$XHH@$=              , .H@@@@MX,
                               .=--------.           -%H.,@@@@@MX,
                               .%MM@@@HHHXX$$$%+- .:$MMX -M@@MM%.
                                 =XMMM@MM@MM#H;,-+HMM@M+ /MMMX=
                                   =%@M@M#@$-.=$@MM@@@M; %M%=
                                     ,:+$+-,/H#MMMMMMM@- -,
                                           =++%%%%+/:-.
```
Ce PoC est basé sur une image dédié [teleport-lab:8](https://quay.io/repository/gravitational/teleport-lab)

Pour une mise en production il faut utiliser les images [teleport-oss](https://quay.io/repository/gravitational/teleport) ou [teleport enterprise](https://quay.io/repository/gravitational/teleport-ent).

## Prérequis :
- [Docker](https://docs.docker.com/get-docker/) ou [Podman](https://podman.io/getting-started/installation)
- [Docker-compose](https://docs.docker.com/compose/install/)

## Mise en oeuvre : 
1. Démarrer le labo:
```sh
docker-compose -f teleport-lab.yml up -d
```
_Note: vous pouvez stoper le labo avec la commande `docker-compose -f teleport-lab.yml down`.__

2. Exploiter la CLI_:
```sh
docker exec -ti term /bin/bash
```
_Note: Toutes les commandes ci dessous seront lancer depuis le container `term`._

- Se connecter au serveur téléport :
```sh
ssh root@luna.teleport
```
- Se connecter à un hôte : 
```
ssh root@mars.openssh.teleport
```
- Lancer Ansible :
```sh
cd /etc/teleport.d/ansible && ansible all -m ping
```
- Tester la command `tsh`, chercher tous les hôtes correspondant au label `env=exemple` et lancer la commande `hostname` :
```sh
tsh ssh root@env=example hostname
```
- Lister tous les hôtes enregistré :
```sh
tsh ls
```
3. Exploiter la WebUI :
```sh
tctl users add testuser --roles=editor,access --logins=root,ubuntu
```
_Note : Cette commande est à lancer depuis le container `term`._

Teleport fournira un url à suivre pour finaliser l'enrollement de l'utilisateur 
```
User "testuser" has been created but requires a password. Share this URL with the user to complete user setup, link is valid for 1h:

https://proxy.luna.teleport:443/web/invite/your-token-here

NOTE: Make sure proxy.luna.teleport:443 points at a Teleport proxy which users can access.
```
Il ne reste qu'a ouvrir cette url dans votre navigateur.

_Note : Avec Chrome il faut utiliser l'argument `--ignore-certificate-errors`._


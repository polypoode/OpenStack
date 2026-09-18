# Projet OpenStack — SecureApp

## Présentation

Mise en place d'une infrastructure **OpenStack** avec **DevStack** sur un serveur Linux.

Le projet consiste à déployer une architecture simple composée de :

* une **DMZ** pour le serveur Web ;
* un **LAN** pour les serveurs applicatif et base de données ;
* un **routeur** permettant la sortie vers le réseau externe ;
* des **Security Groups** pour contrôler les accès ;
* des **machines virtuelles** ;
* une **Floating IP** pour exposer le serveur Web ;
* un **volume persistant** pour la base de données.

### Architecture

```text
                    Internet (external)
                            │
                    ┌───────┴────────┐
                    │  router-prod   │
                    └───────┬────────┘
                            │
            ┌───────────────┼──────────────┐
            │               │              │
    ┌───────▼─────┐  ┌──────▼──────┐       │
    │   DMZ       │  │    LAN      │       │
    │ 10.0.1.0/24 │  │ 10.0.2.0/24 │       │
    └─────────────┘  └─────────────┘       │
            │               │              │
    ┌───────▼──────┐ ┌──────▼──────┐ ┌─────▼──────┐
    │  web-server  │ │ app-server  │ │ db-server  │
    │  10.0.1.10   │ │  10.0.2.10  │ │ 10.0.2.20  │
    │ Floating IP  │ │             │ │            │
    │              │ │             │ │ + Volume   │
    └──────────────┘ └─────────────┘ └────────────┘
```

## Technologies

* OpenStack
* DevStack
* Horizon
* Nova
* Neutron
* Cinder
* Linux
* Git
* OpenStack CLI

## Compétences mises en pratique

* Administration Linux et ligne de commande
* Installation et configuration d'OpenStack
* Virtualisation et gestion de VMs
* Création et configuration de réseaux virtuels
* Routage et adressage IPv4
* Segmentation réseau DMZ / LAN
* Configuration de règles de sécurité réseau
* Gestion des utilisateurs et projets OpenStack
* Gestion des Floating IP
* Gestion du stockage persistant avec Cinder
* Utilisation de la CLI OpenStack
* Analyse et résolution de problèmes liés aux ressources des VMs

## Ressources

Documentation officielle :

* [OpenStack](https://docs.openstack.org/)
* [DevStack](https://docs.openstack.org/devstack/latest/)
* [Horizon](https://docs.openstack.org/horizon/2026.1/)
* [Cinder](https://docs.openstack.org/cinder/rocky/cli/cli-manage-volumes.html)

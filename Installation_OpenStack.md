## Pratique et mise en place

1. Mise en place de OpenStack sur un serveur Debian 24 avec DevStack

À savoir que sur un serveur Ubuntu 26, la mise en place n'est pas encore conforme. Risque de bugs, ce n'est pas supporté.

OpenStack : définition
DevStack : définition

Liste des commandes selon la documentation officielle DevStack

https://docs.openstack.org/devstack/latest/

Update le système

```bash
sudo apt update && sudo apt upgrade -y
```

Ajout d'un user stack et création de /opt/stack

```bash
sudo useradd -s /bin/bash -d /opt/stack -m stack
```

Activation de l'exécution sur /opt/stack

```bash
sudo chmod +x /opt/stack
```

Création d'une règle de no password pour l'utilisateur stack avec Echo. Sudo tee vient écrire la commande echo dans /etc/sudoers.d/stack

```bash
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
```

Accès à l'espace de l'utilisateur stack sans MDP

```bash
sudo -u stack -i
```

Clone le repo de opendev sur l'espace du user stack

```bash
git clone https://opendev.org/openstack/devstack
```

# vérifier les versions disponibles de stack

```bash
git branch -r | grep stable
```

# aller sur la version la plus stable

```bash
git checkout stable/2026.1
```

Si git n'existe pas :

```bash
sudo apt install git
```

On se déplace dans le fichier devstack

```bash
cd devstack
```

Dans le dossier /samples, on trouve un fichier local.conf. Nous avons besoin de certaines configurations de local.conf pour mettre en place notre système.

```bash
cd samples/
cp samples/local.conf .
cd ..
mv samples/local.conf .
```

Ensuite, on modifie certaines lignes de local.conf qui se situe dans devstack

```bash
nano local.conf
```

Je remplace la "secret" par mon mdp

```ini
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD
```

Je décommente la ligne suivante et remplis avec l'IP de mon Ubuntu

```ini
HOST_IP=w.x.y.z
```

Enfin, je lance le programme

```bash
./stack.sh
```

Une fois l'installation terminée, j'ai accès à mon interface ==> via http://MON_IP/dashboard

La dernière étape consiste à faire monter nos variables d'environnement, assignées dans le fichier local.conf.

# commande à faire après l'installation si aucun bug

# vérification et chargement du script dans le shell

```bash
source ~/opt/stack/devstack/openrc admin main
```

Vérifiez d'abord si vous avez bien sourcé openrc avant cette commande

```bash
env | grep OS_
```

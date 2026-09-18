# Challenge OpenStack

## Contexte professionnel

Vous êtes consultant(e) infrastructure chez **TechConseil**. Un nouveau client, **SecureApp**, une scale-up de 80 personnes développant une application SaaS, souhaite migrer de l'hébergement classique vers un cloud privé OpenStack pour :

- Maîtriser ses coûts (factures cloud public explosives)
- Garantir la souveraineté des données (clients européens exigeants)
- Avoir la flexibilité d'un cloud sans dépendance à un provider

Ils disposent d'un serveur **OVH** sous **Proxmox**, sur lequel **OpenStack est déjà installé**. Votre mission est de **déployer et valider l'infrastructure OpenStack** à l'intérieur de cet environnement.

---

## 🎯 Objectifs du challenge (2h max)

L’idée est simple : en 2 heures, vous montez une petite infra OpenStack qui marche, et vous prouvez que ça marche.

Objectifs concrets :
1. Un **réseau DMZ** et un **réseau LAN**
2. **3 VMs** (web, app, db)
3. Un **volume** attaché à la base
4. Des **règles de sécurité** cohérentes
5. Des **tests rapides** + 3 captures d’écran

---

## 📋 Environnement fourni

### Plateforme de départ

Vous avez un serveur **OVH** sous **Proxmox** avec **OpenStack déjà installé**. Vous ne faites pas l’installation*, vous utilisez la plateforme.

### Accès OpenStack

**Dashboard Horizon** : `http://[IP-FOURNIE]/dashboard`

**Identifiants projet** :
- User : `demo` ou votre login fourni
- Password : Le password admin entré dans le fichier de configuration 
- Project : `demo`

**Identifiants admin** (si nécessaire) :
- User : `admin`
- Password : Le password admin entré dans le fichier de configuration
- Project : `admin`

### Ressources disponibles

**Image** :
- ~~`debian-13.1`~~
- `cirros-0.6.3` (vous utiliserez cette image partout

**Réseau externe** :
- `external` ou `public` (déjà configuré pour floating IPs)

**Flavors** :
- `m1.small` (1 vCPU, 2 GB RAM, 20 GB disk)

---

## 🏗️ Architecture à déployer

```
                    Internet (external)
                            │
                    ┌───────┴────────┐
                    │  router-prod   │
                    └───────┬────────┘
                            │
            ┌───────────────┼───────────────┐
            │               │               │
    ┌───────▼─────┐  ┌──────▼──────┐       │
    │   DMZ       │  │    LAN      │       │
    │ 10.0.1.0/24 │  │ 10.0.2.0/24 │       │
    └─────────────┘  └─────────────┘       │
            │               │               │
    ┌───────▼──────┐ ┌──────▼──────┐ ┌─────▼──────┐
    │  web-server  │ │ app-server  │ │ db-server  │
    │  10.0.1.10   │ │  10.0.2.10  │ │ 10.0.2.20  │
    │ Floating IP  │ │             │ │            │
    │              │ │             │ │ + Volume   │
    └──────────────┘ └─────────────┘ └────────────┘
```

---

## 📝 Missions (version allégée)

### Mission 0 : [FACULTATIF] Ajouter un projet et un user

Objectif : Être full-RP et permettre à SecureApp d'avoir son espace dans ce cloud.

1. Création du projet **SecureApp**  
  `openstack project create --domain default --description "Infrastructure Cloud de SecureApp" SecureApp`
2. Création de l'utilisateur `saAdmin`  
  `openstack user create --domain default --password-prompt --project SecureApp saAdmin`  
  Cette commande va demander un mot de passe de connexion pour `saAdmin`
3. Attribution de rôles à saAdmin  
   `openstack role add --project SecureApp --user saAdmin member`

🙂 Le rôle `member` suffit pour la création des assets de cette mise en situation  
🤔 `--domain default` est appliqué sur plusieurs commandes, qu'est-ce ? User, project, domain, quelles différences ?

### Mission 1 : Réseau + routeur

Objectif : avoir une DMZ et un LAN qui sortent vers `external`.

À faire :
- Créer `dmz-network` + `dmz-subnet` en `10.0.1.0/24` (DHCP activé)
- Créer `lan-network` + `lan-subnet` en `10.0.2.0/24` (DHCP activé)
- Créer `router-prod` et le connecter à `external`, `dmz-subnet`, `lan-subnet`

Livrable : capture de la topologie réseau.

---

### Mission 2 : Sécurité (simple et claire)

Objectif : un web exposé, un LAN protégé.

À faire :
- `sg-web` : SSH (22), HTTP (80), ICMP depuis `0.0.0.0/0`
- `sg-lan` : SSH (22), ICMP depuis `0.0.0.0/0`, MySQL (3306) depuis `10.0.2.0/24`

Livrable : capture de la liste des security groups.

---

### Mission 3 : Déployer les VMs

Objectif : 3 VMs conformes à l’architecture.

Paramètres communs :
- Image : `debian-13.1` (ou plutôt `cirros`)
- Flavor : `m1.small`
- Key pair : `keypair-prod` (vous pouvez la créer à la création de la 1ere VM et garder la clé privée)

VMs :
- `web-server` sur `dmz-network` avec `sg-web` + **floating IP**
- `app-server` sur `lan-network` avec `sg-lan`
- `db-server` sur `lan-network` avec `sg-lan`

Livrable : capture de la liste des instances.

---

### Mission 4 : Volume pour la base

Objectif : un volume persistant attaché à `db-server`.

À faire :
- Créer `db-data` (10 GB)
- Attacher à `db-server`

Livrable : capture du volume attaché.

---

### Mission 5 : Tests rapides

Objectif : montrer que ça marche.

Tests minimums :
- Ping + SSH sur la floating IP de `web-server`
- Depuis `web-server`, ping `app-server` et `db-server`
- Depuis `web-server`, tentative de connexion `telnet 10.0.2.20 3306` (doit échouer)

Livrable : 3 lignes de résultats (OK/KO) dans le rendu.

---

## 📊 Livrables attendus (light)

À rendre en un seul fichier (PDF, Word ou Markdown) :
1. 3 captures d’écran : topologie, instances, volume
2. La floating IP du `web-server`
3. Les résultats des tests (OK/KO)

Nom du fichier : `Challenge_OpenStack_[VotreNom].pdf`

---

## 🎤 Restitution rapide (facultatif)

Si on a le temps, 3 minutes :
1. Ce que vous avez réussi à mettre en place
2. Un point qui a coincé
3. Un point que vous avez compris

---

## 💡 Conseils du formateur

- Ne partez pas dans tous les sens, suivez les missions dans l’ordre.
- Un problème ? Levez la main tôt, on gagne du temps.
- Une capture d’écran au bon moment vous évite 20 minutes de stress à la fin.

---

## ❓ Mini‑FAQ

**On peut utiliser la CLI ?**  
Oui. Mais Horizon suffit largement pour ce challenge.

**Pas de floating IP pour app/db ?**  
Normal, elles sont internes. Passez par `web-server` ou la console Horizon.

---

**Bon courage. On est là pour vous aider.**

## Annexes

### Annexe A : Commandes utiles (si CLI)

```bash
# Lister réseaux
openstack network list

# Créer réseau
openstack network create dmz-network

# Créer subnet
openstack subnet create dmz-subnet \
  --network dmz-network \
  --subnet-range 10.0.1.0/24 \
  --gateway 10.0.1.1 \
  --dns-nameserver 8.8.8.8

# Créer routeur
openstack router create router-prod

# Set gateway
openstack router set router-prod --external-gateway external

# Add interface
openstack router add subnet router-prod dmz-subnet

# Créer security group
openstack security group create sg-web

# Ajouter règle
openstack security group rule create --protocol tcp --dst-port 80 sg-web

# Créer VM
openstack server create --image debian-13.1 --flavor m1.small \
  --network dmz-network --security-group sg-web \
  --key-name keypair-prod web-server

# Assigner floating IP
openstack floating ip create external
openstack server add floating ip web-server [FLOATING-IP]

# Créer volume
openstack volume create --size 10 db-data

# Attacher volume
openstack server add volume db-server db-data

# Créer snapshot
openstack server image create web-server --name web-server-backup
```

### Annexe B : Dépannage

**Problème : VM en status ERROR**
- Regarder les logs : Console → Log
- Souvent : flavor trop petit ou quota dépassé
- Solution : Supprimer et recréer avec flavor plus grand

**Problème : Pas de connectivité réseau**
- Vérifier : Router a bien gateway externe
- Vérifier : Router connecté aux subnets
- Vérifier : DHCP activé sur subnet
- Vérifier : Security group autorise ICMP

**Problème : SSH ne fonctionne pas**
- Vérifier : Security group autorise port 22
- Vérifier : Clé SSH correcte (fichier .pem)
- Vérifier : `chmod 600` sur la clé

**Problème : Floating IP ne ping pas**
- Attendre 30 sec (propagation)
- Vérifier : Security group autorise ICMP
- Vérifier : Router a gateway externe


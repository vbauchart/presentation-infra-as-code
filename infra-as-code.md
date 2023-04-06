

background-image: url(img/arolla-backgroud.jpg)
background-size: cover
class: center, middle

.bigtitle[Infra As Code]

<div class="my-footer"><img src="img/dd-man.png" /><p><a href='https://github.com/vbauchart/presentation-infra-as-code'>https://github.com/vbauchart/presentation-infra-as-code</a></p></div>

---
layout: true
name: with-logo
<div class="my-header"><img src="img/logo-arolla.png" /></div>
<div class="my-footer"><img src="img/dd-man.png" /><p>&copy; 2023 Arolla</p></div>
---
layout: false
class: center, middle
template: with-logo

# Petit sondage
---
template: with-logo

# Sondage

## Qui ici maîtrise :
--

### Git

--
### Docker

--
### Ansible

--
### Puppet

--
### Terraform

--
### Kubernetes


[//]: #######################################################################
[//]: #######################################################################

---
layout: false
template: with-logo

# Plan

## 1. La problématique du déploiement d'infrastructure

## 2. Les outils d'Infrastructure as Code

## 3. Les Devs et les Ops sont dans un bateau

# Objectifs

## Comprendre les concepts communs de tous les outils

## Comprendre les enjeux d'organisations et de responsabilité

???


[//]: #######################################################################
[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo


# Problématique par l'exemple

[//]: #######################################################################
---
layout: true
template: with-logo

# Problématique

[//]: #######################################################################
---

## Il faut mettre à jour la configuration SSH sur les 2000 serveurs.

--
### Je me connecte à tous les serveurs un par un ? 🤦

--
### J'utilise un script Bash qui appelle un sed en SSH dans une boucle `for` ? 🤨

[//]: #######################################################################
---
## Les équipes de tests me demandent de créer un environnement identique à la production pour tester de bout en bout.

--
### Je fais une demande des 16 serveurs nécessaires et je configure tout à la main ? 😰

[//]: #######################################################################
---
## Mon site est victime de son succès, il faut passer de 2 à 10 serveurs frontaux !

--
### Je fais une demande pour 8 nouvelles VMs. 👋

--
### J'attend la livraison des VMs par l'équipe système. 🕑

--
### J'installe et configure les 8 VMs 💀

--
### Je déclare les nouvelles machines dans le load-balancer 🤨

--
### Je ne trouve pas la documentation, je cherche le collègue qui a fait la première installation. 🏃

--
### Une fois terminé le site renvoie une erreur une fois sur 10 😰

--
### C'est la panique, je passe la nuit à trouver la virgule en trop qui fait tout planter 😑

--
### 3 mois après, c'est toujours la panique, car j'ai oublié de monitorer les 8 nouvelles machines et une des VM a un problème depuis des semaines et personne ne l'a remarqué 🙃

[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo

# Premières pistes


[//]: #######################################################################
---
layout: false
template: with-logo

# La cible 🎯

## Ne plus faire à la main les tâches répétitives

## Ne pas dépendre d'autres équipes

## Ne pas dépendre d'une documentation périmée

## ➡️ Anticiper les problèmes

## ➡️ Améliorer la confiance

## ➡️ Augmenter la vitesse

[//]: #######################################################################
---
layout: false
template: with-logo

# La méthode ⚙️

## La solution tiens en 3 mots :
--

### Automatiser
--

### Automatiser
--

### Automatiser

.right[![You got it](img/yougotit.gif)]



[//]: #######################################################################
[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo

# Automatisation ?

[//]: #######################################################################
---
layout: true
template: with-logo

# "`Infra As Code`"

[//]: #######################################################################
---
## 🤖 Utiliser un outil de développement

### Choisir le (ou les) bons frameworks adaptés à nos besoins

### Connaître les limites de chaque outil

## 🚩 Versionner

### Ajouter des point de sauvegarde

### Expérimenter 

## 🎠Tester

### Reproduire ce qu'il va se passer

### Vérifier les situations spécifiques

[//]: #######################################################################
---
## Mon site est victime de son succès, il faut passer de 2 à 10 serveurs Web

--
## "Dans le Meilleur des mondes"

### Validation sur l'environnement de test :
```sh
$ git checkout develop                   # branche principale de dev
$ git checkout -b upgrade-servers        # créer branche de test
*$ vim application/web/servers.yml        # modifie la config
$ git commit -am'upgrade to 10 servers'  # ajoute message pertinent
$ git push                               # la CI/CD prend le relais
```

### Suivre le déploiement dans l'application de monitoring ☕

### Tester

### Recommencer

---
## Mon site est victime de son succès, il faut passer de 2 à 10 serveurs Web

## "Dans le Meilleur des mondes"

### Mise en production :

```sh
$ git checkout master                    # branche de release
$ git merge upgrade-servers              # merge la branche
$ git push                               # la CI/CD prend le relais
```

### Suivre le déploiement dans l'application de monitoring ☕

[//]: #######################################################################
---
## Dans cette présentation, nous allons voir les outils suivants :

## 1. Les gestionnaires de configuration

### Puppet

### Ansible

### *Docker*

## 2. Les provisionneurs

### Terraform

### *Ansible*

## 3. Les orchestrateurs

### Kubernetes

### *Ansible*


[//]: #######################################################################
[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo

# Les gestionnaires de configuration


[//]: #######################################################################
---
layout: true
template: with-logo

# Les gestionnaires de configuration
---

## S'execute sur une machine en fonctionnement

### Installe des packages

### Créer des users/groups/répertoires

### Créer des fichiers de configuration

### Lance des commandes de configuration

### Redémarre des services


[//]: #######################################################################

---

## Exemple installation d'un site web sur Debian :

### Installe le paquet `apt install nginx`

### Adapter le fichier `/etc/nginx/conf.d/default.conf` à nos besoins

### Ajouter un utilisateur `app01`

### Redémarrer le service `nginx`

--
## 👉 Un script shell semble pouvoir faire l'affaire !! 👈


[//]: #######################################################################
---

## Un script shell par produit

## Problème résolu 😎

```bash
#!/bin/bash -e

apt install -y nginx

sed 's/^listen 80 /^listen 8080 /' /etc/nginx/conf.d/default.conf

useradd -m app01

systemctl restart nginx
```

--
.image-flash[![Objection](img/objection.gif)]



[//]: #######################################################################
---

## Problèmes à gérer:

### Comment gérer un parc hétérogène (Debian, Redhat, ...) ?

### Comment gérer l'installation **et/ou** la mise à jour ?

### Que se passe-t-il si le serveur n'est pas dans l'état prévu ?

### Comment savoir quels logiciels installer selon le role de la machine ?

### Comment permettre d'avoir plusieurs instances de l'application avec des paramètres différents ?



[//]: #######################################################################
---

## *Petite pause vocabulaire !*

## Idempotence en mathématique

### une fonction a le résultat qu'on l'applique une ou plusieurs fois

### Par exemple, la fonction `abs()` est idempotente :

```math
abs(abs(x)) = abs(x)

abs(abs(-5)) = abs(-5) = 5
asb(abs(abs(-5))) = abs(abs(-5)) = abs(-5) = 5
```

[//]: #######################################################################
---

## Idempotence pour de la configuration :

### Une opération a le même effet qu'on l'applique une ou plusieurs fois

--
```bash
# Indempotent
apt install -y nginx
```

--
```bash
# PAS indempotent
useradd -m app01
```

--
```bash
# PAS indempotent
echo "listen 80" >> /etc/nginx/conf.d/default.conf
```

--
```bash
# Indempotent (si le fichier n'a pas été modifé par ailleurs)
sed 's/^listen 80 /^listen 8080 /' /etc/nginx/conf.d/default.conf
```

[//]: #######################################################################
---

## Pourquoi la notion d'_idempotence_ stricte est importante pour un gestionnaire de configuration ?

### On veut gérer la mise à jour d'un parc de serveurs quelque soit leur état initial

### On veut gérer le cas d'une mise à jour arrêtée brusquement

### La mise à jour ne doit pas être sensible à une intervention manuelle

### On veut vérifier tous les attributs d'un élément, pas seulement son existence


[//]: #######################################################################
---

## Problèmes à gérer:

### Comment gérer un parc hétérogène (Debian, Redhat, ...) ?

### .grey[~~Comment gérer l'installation **et/ou** la mise à jour~~]

### .grey[~~Que se passe-t-il si le serveur n'est pas dans l'état prévu ?~~]

### .red[Comment assurer l’idempotence de chaque déploiement]

### Comment savoir quels logiciels installer selon le role de la machine ?

### Comment permettre d'avoir plusieurs instances de l'application avec des paramètres differents ?


[//]: #######################################################################
---

## Principales fonctionnalités attendues :

### .red[Classifier] les serveurs : leur donner un ou plusieurs rôles

### Créer des .red[ressources systèmes] de façon idempotente (fichiers, repertoires, user, groups, configuration réseau, etc...)

### Créer des fichiers de configuration à partir de .red[templates] à remplir

### Être .red[extensible] par programmation pour s'adapter aux besoins spécifiques


[//]: #######################################################################
---

## Les gestionnaires de configuration traditionnels :

### **Ansible**

### **Puppet**

### .grey[Chef]

### .grey[Salt]

### .grey[...]

### .grey[👉 https://en.wikipedia.org/wiki/Comparison_of_open-source_configuration_management_software]

## Un gestionnaire de configuration un peu spécial :

### Docker

[//]: #######################################################################
[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo

# Ansible

[//]: #######################################################################
---
layout: true
template: with-logo

# Ansible

[//]: #######################################################################
---

## Mode d'installation du contrôleur (`Control Node`)

### Le framework Ansible

### La clef privée SSH

### Les fichiers de description clonés depuis GIT

## Mode d'installation des serveurs supervisés (`Managed Nodes`)

### Python (dans une version [suffisamment récente](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#node-requirement-summary))

### Un utilisateur possédant la clef publique SSH

### Cet utilisateur doit avoir suffisamment de droit sur le système (ex: `sudo`)

> On notera le peu de prérequis nécessaires, en particulier sur les serveurs supervisés

[//]: #######################################################################
---

## Vocabulaire

### On décrit les groupes de `nodes` dans un `inventory`

### On execute un `playbook` sur un un groupe de `nodes`

### Un `playbook` est un fichier .red[YAML] constitué d'une liste de `tasks`

### Une `task` upload et execute un `module` sur chacun des `nodes`

### Un `module` est un morceau de code (généralement .red[Python]) qui s'execute sur le `node`

### Il est possible de regrouper des `tasks` dans un `role`

### Un language de `templates` (Jinja2) est fourni pour créer des fichiers de configuration

???
Inventory statique ou dynamique

[//]: #######################################################################
---
exclude: true
## Résumé

.center[<div class="mermaid">
  flowchart TB

  subgraph nodes
  Inventory --> Group1
  Inventory --> Group2
  Group1 --> Node1
  Group1 --> Node2
  Group2 --> Node2
  Group2 --> Node3
  end

  subgraph tasks
  Playbook --> tasks1
  Playbook --> tasks2
  Playbook --> role1
  Playbook --> role2
  role1    --> task4
  role1    --> task5
  role2    --> task7
  role2    --> task8
  end

  Playbook -.-> Group1
</div>]

[//]: #######################################################################
---

## Pour chaque `playbook`

### Le `playbook` est exécuté localement sur le contrôleur

### Chaque `playbook` vise un groupe de `nodes` 

### Chaque `task` est exécutée l'une après l'autre dans l'ordre

## Pour chaque `task`

### Le code du `module` est uploadé par SSH sur chaque `node` du groupe

### Le `module` est exécuté avec ses paramètres et retourne le résultat à la `task`

### On peut stocker le résultat du `module` dans une variable

### On peut utiliser les variables dans les `tasks` suivantes

[//]: #######################################################################
---

## `modules` essentiels (indempotents) :

### file

### user

### package

### service

### template

### .grey[command] (.red[peut casser l'idempotence!!])

### ...

https://docs.ansible.com/ansible/latest/collections/index_module.html

## + vos propres modules

[//]: #######################################################################
---

## Exemple de `task` :

```YAML
- name: 'Template a file to /etc/file.conf'
  ansible.builtin.template:
    src: 'motd.j2'
    dest: '/etc/motd'
  vars:
    server_role: 'database'
    environment: 'prod'
```

## Contenu de `./templates/motd.j2` :

```Django
Welcome on {{ server_role }}

{% if environment == 'prod' %}
You are on a PRODUCTION server, be careful and don't break anything !
{% else %}
You are on {{ environment }} server.
{% endif %}
```

[//]: #######################################################################
---

## Avantages

### Installation simple sur les `nodes` : Python et un serveur SSH

### Langage de description YAML très simple

### Facilité d'ajout de ses propres modules Python (ou autre langage)

## Inconvénients

### YAML montre ses limites en cas de scénarios complexes

### Ressemble à un langage de programmation sans en être un

### Aucune sécurité par défaut

### Pas de serveur central par défaut

### Attention au passage à l'échelle !

???
- C'est possible que la clef SSH privée soit distribuée à tout le monde, donnant accès à toutes les machines en root !!
- Tentation d'utiliser le module `shell` qui casse tout l'interêt de Ansible
- Il est cependant possible de faire propre avec Ansible avec des règles strictes

[//]: #######################################################################
---
layout: false
template: with-logo

# DEMO

https://github.com/vbauchart/presentation-infra-as-code-ansible-demo

[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo

# Puppet

[//]: #######################################################################
---
layout: true
template: with-logo

# Puppet

[//]: #######################################################################
---

## Mode d'installation du contrôleur

### Le service `puppet master`

### Les fichiers de description clonés depuis GIT

> 📣 L'installation d'une infrastructure Puppet peut être **complexe**.

## Mode d'installation des serveurs supervisés ()

### Un `puppet agent` enregistré auprès d'un `puppet master`

> 📣 L'installation d'un serveur supervisé nécessite que l'agent Puppet soit **déjà** installé. Puppet doit donc être inclus à l'installation par un autre moyen.


[//]: #######################################################################
---

## Vocabulaire

### On décrit les groupes de `nodes` dans un `inventory`

### On execute un `module` sur un un groupe de `nodes`

### Un `manifest` est un fichier .red[.pp] écrit en Puppet (le _DSL_)

### Une `ressource` est la brique de base permettant la modification du système

### Les `ressources` sont regroupées en `class` ou en `defined resources` qui peuvent prendre des paramètres

### Un language de `templates` (ERB) est fourni pour créer des fichiers de configuration


[//]: #######################################################################
---

## Le Langage Puppet

### Comme le YAML de Ansible, il .red[décrit] l'état du système

### Beaucoup plus expressif que YAML, il se rapproche d'un vrai langage de programmation

## Programmation dans Puppet

### Boucles

### Structure conditionnelles

### Variables typées avec une portée de block

### Fonctions (`defined ressources`)

[//]: #######################################################################
---

## `ressources` essentielles (idempotentes) :

### file (avec template)

### user

### package

### service

### .grey[exec] (.red[peut casser l'idempotence!!])

### ...

https://www.puppet.com/docs/puppet/7/type.html

[//]: #######################################################################
---

## Exemple de `ressources`

```Puppet
$server_role = 'database'
$environment = 'prod'

file {
  '/etc/motd':
    content => template('nginx/motd.erb'),
    mode    => '0644',
    owner   => root,
}
```
## Contenu de `./nginx/templates/motd.erb` :

```ERB
Welcome on <%= @server_role %>

<% if @environment == 'prod' %>
You are on a PRODUCTION server, be careful and don't break anything !
<% else %>
You are on <%= @environment %> server.
<% end %>
```

[//]: #######################################################################
---

## Pour chaque `node`

### Le `puppet agent` envoie une requête de mise à jour `puppet master`

### Le `puppet master` .red[compile] tous les fichiers `.pp` associés au `node`

### Le `puppet master` produit un fichier `catalog` contenant une liste de ressources à installer

### Le `puppet agent` reçoit le `catalog` et applique les modifications nécessaires


???
régulierement = toutes les 1/2/3 heures ?

[//]: #######################################################################
---

## Avantages

### Langage proche d'un language de programmation

### Language plus expressif limitant le besoin de créer des ressources sur-mesures

### Chaque node requête le master à intervalle régulier

## Inconvénients

### Plus difficile à administrer que Ansible

### Pas d'orchestration multi-nodes, chaque node est autonome

### Problème de scalabilité à cause du serveur central

[//]: #######################################################################
---
layout: false
template: with-logo

# DEMO

https://github.com/vbauchart/presentation-infra-as-code-puppet-demo

[//]: #######################################################################
[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo

# Docker

[//]: #######################################################################
---
layout: true
template: with-logo

# Qu'est-ce que Docker ?

[//]: #######################################################################
---

## Docker est un système d'isolation de processus utilisant la technologie de `namespace` du noyau Linux

## Un processus dans `namespace` ne peux pas voir les processus d'un autre `namespace`

## Un processus dans `namespace` n'a pas accès aux utilisateurs, aux fichiers, au réseau d'un autre `namespace`

## Alternative légère à la virtualisation 🤩

## Le nom d'un processus dans un `namespace` est un `container`

???
Je simplifie à mort

[//]: #######################################################################
---
layout: false

background-image: url(img/scientifique_casque.jpg)
background-size: cover
class: center, middle

[//]: #######################################################################
---
layout: true
template: with-logo

# Docker est-il un gestionnaire de configuration ?

[//]: #######################################################################
---

## Docker est livré avec un gestionnaire de création d'`image` de disque

### Un mini-langage qui décrit la construction de l'image : `Dockerfile`

### Un système d'héritage d'images pour en créer des nouvelles

### Un système en "couches" (`layers`) qui permet une grande optimisation du stockage et du transfert des images

### Un protocole de `registry` qui permet de stocker des images sur un serveur central


[//]: #######################################################################
---

## Une fois l'`image` Docker généré, elle ne sera plus modifiée et sera distribuée tel quel sur les serveurs d'execution

## Au moment de l'execution:

### Docker peut passer des variables d'environnement au `container`

### Docker peut montrer un "vrai" répertoire dans le `container`

### Docker peut rediriger un "vrai" port vers un `container`

[//]: #######################################################################
---

## Plus besoin d'installer des serveurs, il suffit de récupérer l'`image`

## Chaque serveur n'a besoin que d'un démon Docker et rien d'autre

## On peut lancer plusieurs `containers` sans crainte de conflit

[//]: #######################################################################
---
layout: false
template: with-logo

# DEMO

https://github.com/vbauchart/presentation-infra-as-code-k8s-demo

[//]: #######################################################################
[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo

# Kubernetes

[//]: #######################################################################
---
layout: true
template: with-logo

# Kubernetes

---

## OK, Docker est cool 😎, mais :

### Une fois qu'on a toutes nos `images`, qui les exécute ?

### Comment faire pour que les `containers` se parlent entre eux ?

### Et s'ils sont démarrés sur des machines différentes ?

### Quand un serveur attend ses limites, il faut créer autre serveur pour lancer les `containers` suivants ?

## Comment gérer des containers à l'échelle d'un SI ? 🏗️

[//]: #######################################################################
---
layout: true
template: with-logo

# Fonctionnalités de Kubernetes

---


## Décrire les images toujours exécutés ensemble (`Pod`)

## Créer plusieurs instance d'un même `pod`

## Répartir les requêtes entre les instances de `pod` (`Ingress`)

## Créer des réseaux virtuels associés aux `pods`

## Mettre à jour les `pods` avec les nouvelles images

## Relancer les `pods` défectueux

## Utiliser des configuration .red[YAML] pour stocker toutes ces informations


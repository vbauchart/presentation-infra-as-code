background-image: url(img/arolla-backgroud.jpg)
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

## Qui a déjà entendu parler d'Infra As Code ?
--

### Git

### Docker

### Ansible

### Puppet

### Terraform

[//]: #######################################################################
[//]: #######################################################################
---
layout: false
class: center, middle
template: with-logo


# Problématiques par l'exemple

[//]: #######################################################################
---
layout: true
template: with-logo

# Problématiques

[//]: #######################################################################
---

## Il faut mettre à jour la configuration SSH sur les 2000 serveurs

### Je me connecte à tous les serveurs un par un 🤦
--

### J'utilise un script Bash qui appelle un sed en SSH dans une boucle `for` 🤨

[//]: #######################################################################
---
## Les équipes de tests me demandent de créer un environnement identique à la production pour tester de bout en bout

### Je fais une demande des 16 serveurs nécessaires et je configure tout à la main 😰



[//]: #######################################################################
---
## Mon site est victime de son succès, il faut passer de 2 à 10 serveurs Web

### Je fais une demande pour 8 nouvelles VMs 🕑


### J'installe et configure les 8 VMs 😮‍💨


### Je déclare les nouvelles machines dans le load-balance

--
### Je ne trouve pas la documentation

--
### Une fois terminé le site renvoie une erreur une fois sur 10 😰

--
### C'est la panique, je passe la nuit à trouver la virgule en trop qui fait tout planter 🙃

--
### C'est toujours la panique, car j'ai oublier de monitorer les 8 nouvelles machines et une des VM a un problème

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

## Améliorer la confiance

## Augmenter la vitesse

[//]: #######################################################################
---
layout: false
template: with-logo

# La méthode ⚙️

## La solution tiens en 3 étapes :
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
## 🤖 Utiliser un langage de programmation

### Choisir le langage adapté

### Choisir le bon outil

## 🚩 Versionner

### Ajouter des point de sauvegarde

### Expérimenter

## 🎠Tester

### Reproduire ce qu'il va se passer

### Vérifier les situations spécifiques

[//]: #######################################################################
---
## Mon site est victime de son succès, il faut passer de 2 à 10 serveurs Web

### Validation sur l'environnement de test
```sh
$ git checkout develop                   # branche principale de dev
$ git checkout -b upgrade-servers        # créer branche de test
$ vim application/web/servers.yml        # modifie la config
$ git commit -am'upgrade to 10 servers'  # ajoute message pertinent
$ git push                               # la CI/CD prend le relais
```

--
### Mise en production

```sh
$ git checkout master                    # branche de release
$ git merge upgrade-servers              # merge ce qui a été testé
$ git push                               # la CI/CD prend le relais
```


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

### ...


[//]: #######################################################################

---

## Exemple installation de SSH sur Debian :

### Installe le paquet `apt install openssh-server`

### Génère les clefs du serveur par `ssh-keygen -A`

### Adapter le fichier `/etc/ssh/sshd_config` à nos besoins

### Ajouter un utilisateur

### Redémarrer le démon ssh

--
## 👉 Un script shell semble pouvoir faire l'affaire !! 👈


[//]: #######################################################################
---

## Un script shell par produit

### problème résolu 😎

```script
#!/bin/bash -xe

apt install -y openssh-server

ssh-keygen -A

sed 's/^PermitEmptyPasswords yes/PermitEmptyPasswords no/' /etc/ssh/sshd_config
sed 's/^PermitRootLogin no/PermitRootLogin prohibit-password/' /etc/ssh/sshd_config

useradd -m user1

systemctl restart sshd
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

### Comment permettre d'avoir plusieurs instances de l'application avec des paramètres differents ?



[//]: #######################################################################
---

## *Petite pause vocabulaire !*

## Idempotence en mathématique

### une fonction a le résultat qu'on l'applique une ou plusieurs fois

### Par exemple :

```math
abs(abs(x)) = abs(x)
abs(abs(-5)) = abs(-5) = 5
```

[//]: #######################################################################
---

## Idempotence pour de la configuration :

### Une opération a le même effet qu'on l'applique une ou plusieurs fois

--

## Sur l'exemple précedent du script Bash :

```bash
# Indempotent
apt install -y openssh-server
```

--
```bash
# PAS indempotent
useradd -m user1
```

--
```bash
# Indempotent (si le fichier n'a pas été modifé par ailleurs)
sed 's/^PermitEmptyPasswords yes/PermitEmptyPasswords no/' /etc/ssh/sshd_config
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

### ~~Comment gérer l'installation **et/ou** la mise à jour~~

### ~~Que se passe-t-il si le serveur n'est pas dans l'état prévu ?~~

### **Comment assurer l’idempotence**

### Comment savoir quels logiciels installer selon le role de la machine ?

### Comment permettre d'avoir plusieurs instances de l'application avec des paramètres differents ?


[//]: #######################################################################
---

## Les principaux gestionnaires de configuration traditionnels :

### **Ansible**

### **Puppet**

### Chef

### Salt

### 👉 https://en.wikipedia.org/wiki/Comparison_of_open-source_configuration_management_software

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

## Mode d'installation du client (`Control Node`)

### Le framework Ansible

### La clef privée SSH

### Les fichiers de description dans GIT

## Mode d'installation des serveurs supervisés (`Managed Nodes`)

### Python (dans une version [suffisamment récente](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#node-requirement-summary))

### Un utilisateur possédant la clef publique SSH

### Cet utilisateur doit avoir suffisamment de droit sur le système (ex: `sudo`)

> On notera le peu de prérequis nécessaires, en particulier sur les serveurs supervisés

[//]: #######################################################################
---

## Vocabulaire

### On décrit les groupes de **`nodes`** et leurs paramétrages dans un **`inventory`**

### On execute un **`playbook`** sur un un groupe de serveurs de l'**`inventory`**

### Un **`playbook`** est un fichier YAML constitué d'une liste de **`tasks`**

### Une **`task`** appel un **`module`** avec des paramètres

### Un **`module`** est un morceau de code (généralement Python) qui s'execute sur le **`node`**

### Il est possible de regrouper des **`tasks`** dans un **`role`**

## 

[//]: #######################################################################
---

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

### Le `module` est executé avec ses paramètres et retourne le résultat à la `task`


[//]: #######################################################################
---

## Exemples de **`playbook`**


[//]: #######################################################################
---

## Templates

### Le module `template`

```YAML
- name: 'Template a file to /etc/file.conf'
  ansible.builtin.template:
    src: '/mytemplates/foo.j2'
    dest: '/etc/file.conf'
```

### Des fichiers `Jinja` dans le répertoire `templates`

```Django
Hello {{ name }}!

{% if score > 80 %}
I'm happy to inform you that you did very well on today's {{ test_name }}.
{% else %}
I'm sorry to inform you that you did not do so well on today's {{ test_name }}.
{% endif %}
You reached {{ score }} out of {{ max_score }} points.
```

















[//]: #######################################################################


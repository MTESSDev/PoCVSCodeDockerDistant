<!-- ENTETE -->
[![img](https://img.shields.io/badge/Lifecycle-Experimental-339999)](https://www.quebec.ca/gouv/politiques-orientations/vitrine-numeriqc/accompagnement-des-organismes-publics/demarche-conception-services-numeriques)
[![License](https://img.shields.io/badge/Licence-LiLiQ--P-blue)](https://github.com/CQEN-QDCE/.github/blob/main/LICENCE.md)

---

<div>
    <img src="https://github.com/CQEN-QDCE/.github/blob/main/images/mcn.png" />
</div>
<!-- FIN ENTETE -->


# PoC .Net sur Linux remote 

Étapes pour créer une machine virtuelle Linux sur Azure et se connecter à la machine pour le développement .Net 

Faire logon au portail Azure [https://portal.azure.com](https://portal.azure.com) 

Dans Azure services, créer une nouvelle ressource "Virtual Machine", puis cliquer sur l'option `+ Create`; choisissez l'option `Azure virtual machine`.

La page `Create a virtual machine` est présentée. Elle est composée par plusieurs onglets, qui doivent être répondus pour la collection de toutes les informations utilisées pour la création de la VM. 

## Onglet `Basics`

Dans le premier onglet, `Basics`, il faut répondre aux champs suivants: 

Project details
- Subscription: choisissez votre suscription;
- Resource group: cliques sur `Create new`; puis donnez un nom de votre préférence. Dans cet exemple, on le nomme `CQEN-RG`; 

Instance détails 
- Virtual machine name: dans l'exemple, CQEN-Linux-VM; 
- Region: choisissez la région de votre convenance; 
- Availability options: `Availability zone`; 
- Availability zone: `Zone 1`; 
- Security type: `Standard`; 
- Image: selon votre préférence. Dans cet exemple, on utilisera `Ubuntu Server 20.04 LTS - Gen2`;
- Run with Azure Spot discount: à votre discrétion; 
- Size: à votre discrétion;

Administrator account 
- Authentication type: `SSH public key`
- Username: changez pour votre nom d'utilisateur préféré. Dans cet exemple, on utilisera `cqen`; 
- SSH public key source: `Generate new key pair`
- Key pair name: prenez la suggestion ou donnez un nom de votre préférence. 

Inbound port rules 
- Public inbound ports: `Allow selected ports` 
- Selected inbound ports: choisissez `SSH(22)`, `HTTPS(443)`. 

Cliquez le bouton `"Next: Disks"`

## Onglet `Disks`

Disk options 
- OS disk size: `Default size (30 GiB)`
- OS disk type: `Standard HDD (locally-redundant storage)` ou selon votre préférence. Par contre, l'option du disk SSD est beaucoup plus chère que celle du disk HDD. Dans cet exemple on a choisi HDD étant donné que cette machine n'aura pas une application critique qui doit avoir de la haute disponibilité. 
- Delete with VM: sélectionner
- Encryption type: `(Default) Encryption at-rest with a platform-managed key`

Data disks for CQEN-Linux-VM

- Create and attach a new disk: 
    - Name: prenez la suggestion, ou nommez-le selon votre préférence
    - Source type: `None (empty disk)
    - Size: cliquez sur `change size`, choisissez `Standard HDD (locally-redundant storage) et choisissez la taille qui vous convient. 
    - Encryption type: `(Default) Encryption at-rest with a platform-managed key`
    - Delete disk with VM: sélectionnez la boîte

Cliquez le bouton `"Next: Networking"`

## Onglet `Networking`

Network interface
- Virtual network: `(new) CQEN-RG-vnet`
- Subnet: `(new) default (10.1.0.0/24)`
- Public IP: `(new) CQEN-Linux-VM-ip
- NIC network security group: `Basic`
- Public inbound ports: `Allow selected ports`
- Select inbound ports: choisissez `SSH(22)`, `HTTPS(443)`. 
- Delete public IP and NIC when VM os deleted: sélectionnez la boîte

Load balancing 
- Load balancing options: `None`

Cliquez le bouton `"Next: Management"`

## Onglet `Management` 

Monitoring
- Boot diagnostics: `Enable with managed storage account (recommended)`

Auto shutdown
- Enable auto-shutdown: sélectionnez la boîte

Guest OS updates 
- Patch orchestration options: `Image default`

Cliquez le bouton `"Next: Advanced"`

## Onglet `Advanced`

On ne va pas configurer des options dans cet onglet. 

Cliquez le bouton `"Next: Tags"`

## Onglet `Tags`

- Name: `Serveur Linux`
- Value: `Develop`

Cliquez le bouton `"Next: Review + Create"`

## Onglet  `Review + Create`

À cet onglet, Azure va procéder à des validations des options choisies dans les onglets précédants, et si le tout est correct, il vous montrera le message `Validation passed`. En plus, il affichera toutes les configurations faites, les détails du produit que vous déployez, bien comme le cout et les termes de service. 

Si vous êtes d'accord avec ces termes, cliquez le bouton `Create` en bas à gauche de l'écran. 

Vous verez un écran appellé `Generate new key pair`, qui vous demande de faire le téléchargement de la clé privée SSH de votre utilisateur, pour la connection remote. Cliquez sur le bouton `Download private key and create resource`. Prenez note du répertoire où vous enregistrez ce fichier. Après le telechargement, Azure continue la création et le déploiement de votre machine virtuelle. Cela peut prendre quelques minutes, merci de patienter pendant le traitement de la demande. 

Après quelques minutes, vous recevez le message `Your deployment is complete`, ce que nous confirme que la création est réussie. Cliquez sur le bouton `Go to resource` pour voir les ressources créés. 

# Connecter à la machine

Dans la page de votre VM, prenez note de l'adresse IP publique de votre machine (Public IP address). Assurez-vous aussi que votre machine soit démarrée. 

<img src="./images/AzurePublicIP.png" />

Vous allez vous connecter à cette machine via une connection SSH. Dans votre machine locale, ouvrez votre VSCode. 

### Installation de l'extension 

Dans la barre d'outils à gauche du VSCode, cliquez sur l'icône des Extensions; ensuite, dans la boîte de recherche, cherchez le terme `Remote Development`, l'extension se présentera dans la liste à gauche. Cliquez sur installer et attendez la fin de l'installation. 

<img src="./images/VSCodeExtension.png" />

Une fois finie, on va se connecter à la machine virtuelle accessible via l'adresse IP publique que vous avez pris note, avec la clé qui a été générée dans l'étape `Onglet Review+Create` ci-dessus. 

D'abord, vérifiez si vous possedez le répertoire de configuration du SSH dans votre profil d'usager. Il est important parce que c'est l'endroit à enregistrer vos clés d'accès à la machine virutelle.  

```bash 
$ ls ~/.ssh 
```

Si cette commande répond que le répertoire n'est pas trouvable, il faut le créer avec la commande ci-dessous: 

```bash 
$ mkdir ~/.ssh
```

Ensuite, faites la copie du fichier que vous avez téléchargé dans ce répertoire nouvellement créé. Dans mon cas, le fichier s'appele `CQEN-Linux-VM_key.pem` et il a été téléchargé originalement dans le repertoire `~/Downloads`: 

```bash
$ mv ~/Downloads/CQEN-Linux-VM_key.pem ~/.ssh/.
$ chmod 400 ~/.ssh/CQEN-Linux-VM_key.pem
```

Maintenant, dans VSCode, faites `Ctrl+Shift+P` pour ouvrir le menu de selection d'options, puis saisissez `Remote-SSH:` ; vous allez voir l'option `Add a New SSH Host`. Cliquez sur l'option. 

<img src="./images/VSCodeOptionMenu.png" />

Ensuite, dans le dialog qui suit `Enter SSH connection command`, saississez la commande qui suit, en substituant le nom d'usager, l'IP publique et le path vers la cl'publique avec les valeurs que vous avez configurés: 

<img src="./images/SSHCommande.png" />

```bash 
$ ssh cqen@22.196.64.15 -A -i ~/.ssh/CQEN-Linux-VM_key.pem
```

Dans le dialogue de plateforme, choississez `Linux`: 

<img src="./images/SSHPlateforme.png" />

Dans le dialog `Select SSH configuration file to update`, saississez l'adresse suivante: 

<img src="./images/SSHPKpath.png" />

```bash
~/.ssh/config
```

Et c'est fait, votre configuration de connection est créé. 

Pour vous connecter, faites `Ctrl+Shift+P`, saisissez `Remote-SSH`, et choississez l'option `Connect current window to host`; 

<img src="./images/SSHConnect01.png"/>

Choisissez votre serveur par l'adresse IP: 

<img src="./images/SSHConnect02.png"/>

À la première connexion, on vous demandera de confirmer l'empreinte de la clé publique; confirmez en cliquant sur `Oui`. 

<img src="./images/SSHConfirmation.png"/>

Attendez quelques secondes avant que la connexion soit établie. Toute action que vous ferez dorénavant dans cette session du VSCode se passera dans le host remote. 

# Préparation de la machine distante 

Prérequis
- Configurer l'usager admin; bloquer l'accès de root;
- Installer .netCore SDK;
- Installer docker 

## Configurer nouveau usager 

Il faut configurer un nouveau usager, qui n'a pas le profil du root, et qui soit dans le groupe de sudoers, pour des accès réhaussés. Par exemple, si l'on créé ce nouveau usager appellé  `labuser` 

```bash
$ adduser labuser 
$ adduser labuser sudo
```

## Installer dotnet core SDK framework

Suivez les instructions d'installation du SDK framework dotnet core 6 à partir de la doc de Microsoft: https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu#2204

Assurez-vous de suivre les infos de la version 22.04, et seulement l'instalation du SDK 'Install SDK'.

## Installez docker 

Suivez les instructions d'installation de docker à partir des liens suivants: 

- https://docs.docker.com/engine/install/ubuntu/
- https://docs.docker.com/engine/install/linux-postinstall/

Dans le poste local: 

- Installez l'extension Docker;

Après l'installation, faites un reboot de votre machine virtuelle. 

# Créez une nouvelle application dotnet dans une machine remote

Pour créér la nouvelle application dotnet, suivez les étapes ci-dessous: 

```bash
$ mkdir projects
$ cd projects 
$ dotnet new webapp -o poclinuxdotnet --no-https -f net6.0
# Pour l'execution de l'application dans la machine distante
dotnet watch 

# Attendre le redirect vers le navigateur. 
```

## Dockeriser l'application

Faire le l'inclusion du fichier Dockerfile et .dockerignore dans le répertoire du projet. 

Ensuite faire le build de l'application: 

```bash
$ docker build -t poclinuxdotnet .
```

L'image sera tagée comme poclinuxdotnet. Attendre le build finir l'application. 

# Debug d'une application dans une machine remote 

## Prérequis pour une session de débogage

Dans le poste local: 

- VSCode;
- Extension C#;

## Débogage 

Ouvrez la palette de commande (Ctrl+Shift+P) et saisissez `Docker: Add docker files to workspace.`. Suivez les instructions à l'écran. 

Changez vers la vue  `Run and Debug view` (Ctrl+Shift+D).

Selectionnez la configuration `Docker .NET Core Launch`.

Mettez votre breakpoint ou li fait du sens pour vous.

Commencez le debugging! (F5)

# Troubleshoot 

**I'm on Linux and get the error "connect EACCES /var/run/docker.sock"**

Since VS Code runs as a non-root user, you will need to follow the steps in "Manage Docker as a non-root user" from [Post-installation](https://aka.ms/AA37yk6) steps for Linux for the extension to be able to access docker.

**Problème EACESS dans porte**

Lister les procès qui sont ratachés à la porte en problème avec la commande `fuser <port>/tcp`. 

Par exemple, si l'on veut débloquer la porte 8080

```bash
$ fuser 8080/tcp -k
```
# Références

**Installation**

https://tecadmin.net/how-to-install-dotnet-core-on-ubuntu-22-04/
https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu - Installation de .Net 6


**Installation 3.1** 

https://abhisheksubbu.github.io/dotnet-core-install-ubuntu-20-04-lts/


**APP de test  (tutorial)**

https://dotnet.microsoft.com/en-us/learn/aspnet/hello-world-tutorial/intro

**Dockeriser application dotnet**

Install docker sur ubuntu
https://docs.docker.com/engine/install/ubuntu/
https://docs.docker.com/engine/install/linux-postinstall/



Dockerisation d'un aspnet 
https://docs.docker.com/samples/dotnetcore


**Debug**

Debug de l'app
https://code.visualstudio.com/docs/containers/debug-netcore


https://jasonwatmore.com/post/2021/06/24/vs-code-net-debug-a-net-web-app-in-visual-studio-code
<!-- 
sudo docker build -t test:1.0 .
sudo docker run -d -p 80:80 --name myapp test:1.0 
-->


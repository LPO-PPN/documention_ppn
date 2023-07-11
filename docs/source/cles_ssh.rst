Sécurisation des connexions via SSH
===================================

Créer sa paire de clé (privée/publique)
---------------------------------------

Pour se connecter à un serveur en SSH, je dois posséder une **clé privée**, qui fonctionne en lien avec une **clé publique**.

La **clé publique** doit s'inclure dans un fichier *d'autorisation de clé* sur le serveur.
La **clé privé** est utilisée par l'utilisateur au moment de la connexion, avec une *phrase de passe (ou passphrase)*, et s'authentifie avec la *clé publique*

Pour créer une paire de clé, nous allons ouvrir l'utilitaire **puttygen.exe** fourni avec le téléchargement de `PuTTy <https://www.putty.org/>`_.
Une fois ouvert, faire les étapes suivantes :

- Vérifier que la case RSA est sélectionnée en bas, et que le nombre 2048 est inscrit en bas à droite. Dans le cas contraire, modifier ces infos pour qu'elles prennent ces valeurs.
- Cliquer sur **"Generate"**
- Faire glisser la souris partout sur la zone blanche jusqu'à ce que la barre de progression verte arrive à la fin
- Plusieurs informations devrait être affichés. A noter que l'on peut mettre ce que l'on veut dans l'espace *"Key comment"*.

  - Entrer la **passphrase** (**ATTENTION, LIRE LA NOTE CI-DESSOUS**)
  - Confirmer la **passphrase**

.. WARNING::

    **PASSPHRASE**

    Concernant la passphrase, il s'agit d'un mot de passe qui se veut long, connu seulement de vous-même et A RETENIR ! Il serviera à s'authentifier auprès de la clé publique.

- Finir par cliquer sur le bouton **"Save private key"**. Enregistrer alors le fichier en .ppk où l'on souhaite, avec le nom que l'on souhaite. **=> CECI EST NOTRE CLE PRIVEE**

.. IMPORTANT::

    **Garder l'application puttygen ouverte !!!**


Enregistrer votre clé publique sur le serveur
---------------------------------------------

.. NOTE::

    Si vous n'avez pas encore accès à votre serveur, contactez votre hébergeur pour inclure votre clé publique sur le serveur.

Nous allons maintenant mettre notre clé publique sur le serveur... mais où est-elle ???
Encore sur puttygen ;)

- Copier la clé publique qui se situe dans le cadre en dessous de "Public key for pasting into OpenSSH authorized_keyx file"
- Maintenant, accéder au *Terminal* de notre serveur
- Effectuer les commandes suivantes : 

.. code-block:: bash

    sudo su <votreutilisateur>

    sudo apt update # Mise à jour et installation des utilitaires principaux
    sudo apt upgrade -y # Mise à jour et installation des utilitaires principaux
    sudo apt install ssh # Installer ssh s'il n'existe pas déjà

    sudo chmod 755 /home/<votreutilisateur>/ # IMPORTANT Garder au moins les permissions [Autre / X] d'activé, sans ça GeoNature ne fonctionnera plus...
    mkdir /home/<votreutilisateur>/.ssh # S'il n'existe pas
    sudo chmod 700 /home/<votreutilisateur>/.ssh
    nano /home/<votreutilisateur>/.ssh/authorized_keys
    # Clique-droit pour coller votre clé publique, puis Ctrl+S, puis Ctrl+X
    sudo chmod 600 /home/<votreutilisateur>/.ssh/authorized_keys

    sudo systemctl restart sshd

.. IMPORTANT::

    SSH est très pointilleux sur les permissions, d'où la redefinission des permissions sur notre dossier utilisateur.

    A NOTER : Les dossiers et fichiers doivent appartenir au même groupe que l'owner (votre utilisateur)

Utliser la clé privé avec WinSCP
--------------------------------

- Cliquer sur "Nouveau site"
- Entrer le nom d'hôte (Adresse ip) et <votreutilisateur> dans Nom d'utilisateur / Protocole de fichier SFTP
- Cliquer sur le bouton "Avancé"
- Naviguer dans "SSH" > "Authentification" > "Paramètres d'authentification" > "Fichier de clé privée" : Cliquer sur "..." et pointer sur l'emplacement de notre clé privée
- Cliquer sur "OK"
- Ne pas oublier de changer le port si ce n'est plus 22
- Cliquez sur "Sauver"
- Enfin, connectons-nous !

Utliser la clé privé avec PuTTy
-------------------------------

- Entrer le nom d'hôte (Adresse ip) et le port si autre que 22 / Connection type SSH
- Entrer un nom pour identifier la connexion dans "Saved Sessions"
- Naviguer dans "Connection" et entrer <votreutilisateur> dans "Logical name of remote host"
- Naviguer ensuite dans "Connection" > "SSH" > "Auth" : Cliquer sur le bouton "Browse..." et pointer sur l'emplacement de notre clé privée
- Revenir sur "Session" et cliquer sur le bouton "Save" (nous avons enregistrer notre configuration)
- Enfin, connectons-nous avec "Open" !

Utliser la clé privé avec DBeaver
---------------------------------

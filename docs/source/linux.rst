Tutos Linux (Admin)
=======================

Quelques commandes intérssantes
-------------------------------------------------------------------------

Afficher toutes les partitions et leurs tailles en Go
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  df -h

- Afficher le contenu d'un dossier et la taille des dossiers contenus en Go

::

   ls <chemin jusqu'au dossier> # Liste l’ensemble des fichiers et dossiers du répertoire dans lequel vous vous trouvez
   ls -l # permet un affichage détaillé (permissions, taille…)
   sudo du -h -d1 -x <chemin du dossier> # Indique le contenu répertoire d'un dossier avec sa taille
   
Afficher le Gestionnaire des taches
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  top # ou
  htop # sudo apt-get install htop
  
Se déplacer dans les dossiers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    cd # permet de revenir au répertoire /home/utilisateur (identique à cd ~)
    cd - # permet de revenir au répertoire précédent
    cd .. # permet de remonter au répertoire parent (ne pas oublier l'espace contrairement à windows)
    cd / # permet de remonter à la racine de l'ensemble du système de fichiers
    cd /<chemin absolu vers le dossier> # ou cd <chemin absolu vers le dossier> -> se place dans le répertoire /<chemin absolu vers le dossier>
    
.. NOTE::

  Astuce : Appuyer sur `TAB` pour l'auto-complétion de votre chemin ou de vos commandes
  

Créer/Copier/Déplacer/Supprimer des fichiers/dossiers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  mk <chemin vers le futur fichier> # Créer un fichier
  mkdir <chemin vers le futur dossier> # Créer un dossier
  
  cp /home/<nom_utilisateur>/dossier1/monfichier /home/<nom_utilisateur>/dossier2/monfichier # Copier le fichier du dossier 1 dans le dossier 2
  cp monfichier monnouveaufichier # Duplique le fichier monfichier avec le noveau nom monnouveaufichier
  
  mv /home/<nom_utilisateur>/dossier1/monfichier /home/<nom_utilisateur>/dossier2/monfichier # Déplace le fichier du dossier 1 au dossier 2
  mv monfichier nouveaunom # Déplace le fichier en le renommant
  
  rm <chemin jusqu'au fichier> # Supprimer le fichier
  rmdir <chemin jusqu'au dossier> # Supprimer le dossier - DOIT ETRE VIDE
  rm -R <chemin jusqu'au dossier> # Supprimer le dossier récursivement
  
.. WARNING::

  Attention à l’utilisation de `mv` ou de `rm`, ils suppriment et écrasent les fichiers sans demander de confirmation.

Gérer les permissions
~~~~~~~~~~~~~~~~~~~~~

::

    # CHMOD : Modifie les permissions d'accès à un fichier ou à un répertoire.
    # 2 manières d'écrire : avec ou sans les vaeurs dites octales (= numériques)
    # + : Ajoute une permission // - : Enlève une permission // = : Autorise uniquement l'autorisation indiquée
    # r : Lecture ; Valeur octale 4 // w : Écriture ; Valeur octale 2 // x : Exécution ; Valeur octale 1 // Aucune permission ; Valeur octale 0
    # u : Propriétaire du fichier // g : Groupe propriétaire du fichier // o : Tous les autres utilisateurs
    # Possibilité d'ajouter ses permissions de façon recurssive avec chmod -R xxxxx
    
    chmod u=rw,go=r <MonFichierOuRep>
    chmod 644 <MonFichierOuRep> # Exactement la même chose que ci-dessus mais en utilisant les valeurs octales (Nota : 6 = 4+2 = lecture + écriture)
    # Dans l'ordre u-g-o, u = 6, g = 4, o = 4, traduit en u = rw, g = r et o = r, raccourci en u=rw,go=r
   
    chmod u=rw,g=r <MonFichierOuRep> # Fixe l'autorisation d'ouverture et de lecture de MonFichier au propriétaire, uniquement la lecture au groupe et interdit tout accès aux autres.
    chmod 640 <MonFichierOuRep> # Exactement la même chose que ci-dessus mais en utilisant les valeurs octales
    
    # CHOWN : Change le propriétaire et le groupe propriétaire d'un fichier.
    # Possibilité d'ajouter ses permissions de façon recurssive avec chown -R xxxxx
    
    chown autreUtilisateur <MonFichier> # Change le propriétaire de MonFichier en autreUtilisateur
    chown -R lui:nous <MonRep> # Change le propriétaire en lui et le groupe propriétaire en nous du répertoire <MonRep> ainsi que tout ce qu'il contient
    
    # CHGRP : Change le groupe propriétaire d'un fichier.
    # Possibilité d'ajouter ses permissions de façon recurssive avec chgrp -R xxxxx
    # Option -h : Change le groupe propriétaire d'un lien symbolique et seulement lui (ne touche pas à la destination du lien)
    
    chgrp unGroupe <monFichier> # Change le groupe propriétaire du fichier MonFichier en unGroupe
    chgrp -R unGroupe <monRep> # Change le groupe propriétaire du répertoire monRep ainsi que tout ce qu'il contient en unGroupe
 
>>>>> Il existe tout plein d’autres commandes qui peuvent être utiles, n’hésitez pas à consulter cette doc : https://doc.ubuntu-fr.org/tutoriel/console_commandes_de_base

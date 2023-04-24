Tutos Linux (Admin)
=======================

Quelques commandes intérssantes
-------------------------------------------------------------------------

- Afficher toutes les partitions et leurs tailles en Go

::

  df -h

- Afficher le contenu d'un dossier et la taille des dossiers contenus en Go

::

   ls <chemin jusqu'au dossier> # Liste l’ensemble des fichiers et dossiers du répertoire dans lequel vous vous trouvez
   ls -l # permet un affichage détaillé (permissions, taille…)
   sudo du -h -d1 -x <chemin du dossier> # Indique le contenu répertoire d'un dossier avec sa taille

- Créer/Supprimer des fichiers/dossiers

::

  mk <chemin vers le futur fichier> # Créer un fichier
  mkdir <chemin vers le futur dossier> # Créer un dossier
  rm <chemin jusqu'au fichier> # Supprimer le fichier
  rmdir <chemin jusqu'au dossier> # Supprimer le dossier - DOIT ETRE VIDE
  rm -R <chemin jusqu'au dossier> # Supprimer le dossier récursivement

- Gérer les permissions

::

  

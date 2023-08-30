Développer un module MODULATOR GeoNature
========================================

| source `Dépot et installation (github PnX-SI) <https://github.com/PnX-SI/gn_modulator>`_ (Documentation officielle)
| source `Aide au développement d'un sous-module <https://github.com/PnX-SI/gn_modulator/blob/main/doc/creation_module.md>`_ (Documentation officielle)

----------
Le concept
----------

A compléter...

--------------
Fonctionnement
--------------

Ce module GeoNature est un générateur de modules, qui permet construire dynamiquement des sous-modules disposant de leur propre modèle de données, à partir de fichiers de configuration YAML.
Chaque sous-module dispose d'une page d'accueil avec une carte, liste et filtres des objets du sous-module.

-----------------------------
Installer le module Modulator
-----------------------------

1. Télécharger le projet à la racine de l'utilisateur de votre GeoNature (``cd``)

::

    cd
    wget https://github.com/PnX-SI/gn_modulator/archive/X.Y.Z.zip
    unzip X.Y.Z.zip
    rm X.Y.Z.zip
    mv ~/gn_modulator-X.Y.Z ~/gn_modulator

2. Installer le module

::
    
    source ~/geonature/backend/venv/bin/activate
    geonature install-gn-module ~/gn_modulator MODULATOR
    sudo systemctl restart geonature
    deactivate

.. WARNING::

    Il faut vérifier que le dossier `/home/geonatureadmin/gn_modulator/config/modules/contrib/` existe, et que les liens symboliques vers les sous-modules existent si l'on développe dans le dossier `contrib`.
    Dans le cas contraire, effectuer les commandes suivantes : `mkdir /home/geonatureadmin/gn_modulator/config/modules/contrib/` puis `ln -s /home/geonatureadmin/gn_modulator/contrib/* /home/geonatureadmin/gn_modulator/config/modules/contrib/`.

--------------------
Créer un sous-module
--------------------

A compléter...

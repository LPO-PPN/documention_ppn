Développer un module monitorings GeoNature (Atelier du 25 mai 2023)
===================================================================

| source `Création d'un sous-module <https://github.com/PnX-SI/gn_module_monitoring/blob/master/docs/sous_module.rst>`_ (Documentation officielle du module monitorings)

--------------
Fonctionnement
--------------

Le module Monitoring est un module générique de gestion des données de protocoles de type suivis.
Il permet de générer des sous-modules (stockés dans la table gn_commons.t_modules) pour chaque protocole de suivi.

Au sein de ces sous-modules, nous retrouvons un niveaux d'information bien défini : 

- *(Facultatif)* **GROUPES DE SITES** Un groupe de sites est composé d'un ou plusieurs sites
- **SITES** (nom, type, localisation) Un site est composé d'une ou plusieurs visites
- **VISITES** (dates, observateurs) Une visite est composée d'une ou plusieurs observations
- **OBSERVATIONS** (espèce, dénombrement) Une observation est composée d'un ou plusieurs détails d'observation
- *(Facultatif)* **DETAIL DES OBSERVATIONS** Donne un détail à l'observation (permet d'ajouter un niveau supplémentaire dans la hierarchie)

Nous verrons plus en détail ces niveaux dans la partie `Configuration des niveaux <#configuration-des-niveaux>`_

.. NOTE::

    Le module est actuellement en refonte d'une partie de son modèle de données. Le fonctionnement est valable pour les versions <=0.5.0.

.. image:: _static/gn_monitoring/2020-06-MCD-monitoring.jpg
    :alt: MCD du schema gn_monitoring

-----------------------------
Installer le module Monitorings
-----------------------------

- **Depuis la 2.11**

- **Antérieur à la 2.11**

--------------------
Créer un sous-module
--------------------

Configuration générale
~~~~~~~~~~~~~~~~~~~~~~~~~

- **L'Arboresence du sous-module** ``config.json``

- **Les Objets du module** ``module.json``

- **Les Nomenclatures utilisées** ``nomenclature.json``

Configuration des niveaux
~~~~~~~~~~~~~~~~~~~~~~~~~

- **Les Groupes de site** ``group_site.json``

- **Les Sites** ``site.json``

- **Les Visites** ``visit.json``

- **Les Observations** ``observation.json``

- **Les Détails d'une observation** ``observation_detail.json``

Configuration des exports
~~~~~~~~~~~~~~~~~~~~~~~~~

- **La Vue de synthèse** ``synthese.sql``

- **Paramétrage des exports** ``module.json``

Définir ses variables dans la configuration des niveaux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- **Les variables génériques (par défaut)**

- **Les variables additionnelles (créées sur mesure)**

- **Les variables dynamiques**

Gérer les permissions (<= 0.5.0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Dépot des projets de sous-modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Un repository GitHub a été mis en place par l'équipe de développement pour recenser tous les projets de sous-modules qui ont été paratagés à la communauté.

Le lien du dépot : https://github.com/PnX-SI/protocoles_suivi

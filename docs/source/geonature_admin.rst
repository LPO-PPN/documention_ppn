Tutos GeoNature (Admin)
=======================

Montée en version GeoNature 2.10.4 vers GeoNature 2.11.2, par @maximetoma
-------------------------------------------------------------------------

1/ Taxhub
~~~~~~~~~

- Mettre à jour TaxHub en version 1.11.1

::

  cd
  wget https://github.com/PnX-SI/TaxHub/archive/1.11.1.zip
  unzip 1.11.1.zip
  mv taxhub taxhub_old
  mv TaxHub-1.11.1/ taxhub
  rm 1.11.1.zip
  cp taxhub_old/settings.ini taxhub/settings.ini
  cp taxhub_old/apptax/config.py taxhub/apptax/config.py
  cp taxhub_old/static/app/constants.js taxhub/static/app/constants.js
  cp -aR taxhub_old/static/medias/ taxhub/static/
  cd taxhub
  ./install_app.sh

- Suite à la correction d'un code de département, il est fortement conseillé de relancer le peuplement des données de la table bdc_statut_cor_text_area en utilisant la commande suivante (après avoir mis à jour GeoNature en version 2.11 ou plus, si vous utilisez celui-ci) :

::

  cd ~/taxhub
  source venv/bin/activate
  flask taxref link-bdc-statut-to-areas

- Si vous souhaitez mettre à jour Taxref, utilisez les scripts présents dans le dossier /apptax/taxonomie/commands/migrate_taxref
- VERIFIER / SAUVEGARDER LES ESPECES HORS TAXREF

::

  flask taxref migrate-to-v16 test-changes-detection
  flask taxref migrate-to-v16 import-taxref-v16
  
- Si vous mettez à jour TaxHub, assurez-vous que vous n'ayez pas de vues spécifiques qui dépendent des tables supprimées
- Relancer le service

::

  sudo systemctl reload taxhub

2/ UsersHub
~~~~~~~~~

- Mettre à jour UsersHub

::

  cd
  wget https://github.com/PnX-SI/UsersHub/archive/2.3.3.zip
  unzip 2.3.3.zip
  rm 2.3.3.zip
  mv /home/`whoami`/usershub/ /home/`whoami`/usershub_old/
  mv UsersHub-2.3.3 /home/`whoami`/usershub/
  cp /home/`whoami`/usershub_old/config/config.py /home/`whoami`/usershub/config/config.py
  cp /home/`whoami`/usershub_old/config/settings.ini /home/`whoami`/usershub/config/settings.ini
  cd usershub
  ./install_app.sh

- Attention si vous avez modifiez certains paramètres dans le fichier config.py tels que les paramètres de connexion à la base de données, ils seront écrasés par les paramètres présent dans le fichier settings.ini
- Relancer le service

::

  sudo systemctl start usershub


3/ GeoNature
~~~~~~~~~

- Mettre à jour GeoNature

::

  wget https://github.com/PnX-SI/GeoNature/archive/2.11.2.zip
  unzip 2.11.2.zip
  rm 2.11.2.zip
  mv /home/`whoami`/geonature/ /home/`whoami`/geonature_old/
  mv GeoNature-2.11.2 /home/`whoami`/geonature/
  cd geonature

  ./install/migration/migration.sh

- Attention, le script de migration de la version 2.11 a une régression et ne récupère plus automatiquement la configuration des modules fournis avec GeoNature (Occtax, Occhab, Validation). Rapatriez manuellement vos éventuels fichiers de configuration de ces modules si vous en avez créé :

::

  cp geonature_old/contrib/occtax/config/conf_gn_module.toml geonature/contrib/occtax/config/conf_gn_module.toml
  cp geonature_old/contrib/gn_module_validation/config/conf_gn_module.toml geonature/contrib/gn_module_validation/config/conf_gn_module.toml
  cp geonature_old/contrib/gn_module_occhab/config/conf_gn_module.toml geonature/contrib/gn_module_occhab/config/conf_gn_module.toml

- Relancer le service

::

  sudo systemctl start geonature

.. WARNING::

  Erreur Proxy 502 sur l’appel de http://xxxxxxxx/geonature/api/gn_commons/modules
  N’A PAS CREER DE DOSSIER EXTERNAL MODULES DANS GEONATURE !
  Copier/Coller de OLD vers NEW, faire un update_configuration et relancer le service ne résout pas le problème

  ->	PROBELEME TIMEOUT_GUNICORN TROP FAIBLE

- Si vous les utilisez, mettre à jour Dashboard en version 1.2.1 (ou plus) et Monitoring en version 0.4.0 (ou plus), après la mise à jour de GeoNature
- Si vous mettez à jour Taxref et que vous utilisez GeoNature, mettez à jour les règles de sensibilité suite à la mise à jour de Taxref :

::

  source geonature/backend/venv/bin/activate
  geonature sensitivity refresh-rules-cache

4/ Monitoring
~~~~~~~~~

- Si vous mettez à jour le module, il vous faut passer à Alembic.
- Pour cela, une fois la version 2.11 (ou plus) de GeoNature installée :

::

  wget https://github.com/PnX-SI/gn_module_monitoring/archive/0.4.1.zip
  unzip 0.4.1.zip
  rm 0.4.1.zip
  mv /home/`whoami`/gn_module_monitoring /home/`whoami`/gn_module_monitoring_old
  mv /home/`whoami`/gn_module_monitoring-0.4.1 /home/`whoami`/gn_module_monitoring
  cp /home/`whoami`/gn_module_monitoring_old/config/conf_gn_module.toml  /home/`whoami`/gn_module_monitoring/config/conf_gn_module.toml
  rsync -av /home/`whoami`/gn_module_monitoring_old/config/monitoring/ /home/`whoami`/gn_module_monitoring/config/monitoring/ --exclude=generic
  source /home/`whoami`/geonature/backend/venv/bin/activate

  # Entrer dans le virtualenv : 
  source ~/geonature/backend/venv/bin/activate
  # Installer la nouvelle version de Monitoring avec le paramètre --upgrade-db=false : 
  geonature install-gn-module --upgrade-db=false /home/geonatureadmin/gn_module_monitoring MONITORINGS
  # Indiquer à Alembic que votre base de données est en version 0.3.0 : 
  geonature db stamp 362cf9d504ec  # monitorings 0.3.0
  # Mettre à jour la base de données en version 0.4.0 : 
  geonature db upgrade monitorings@head
  sudo systemctl reload geonature

.. WARNING::

  Erreur 404 sur https://sep-geonature.reserves-naturelles.org/api/monitoring/config
  Même erreur sur base test

  -> Résolu : Dans la table gn_commons.t_modules, changer le module_code de monitoring à **monitorings**

5/ Exports
~~~~~~~~~

- RAS – 1.3.0 déjà installée

6/ Dashboard
~~~~~~~~~

- RAS – 1.2.1 déjà installée


Montée en version GeoNature 2.11.2 vers GeoNature 2.12.2, par @maximetoma
-------------------------------------------------------------------------

1/ Téléchargement de Export, Dashboard et Monitoring
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Mettre à jour les modules Export en version 1.4.0, Dashboard en version 1.3.0 (ou plus) et Monitoring en version 0.5.0 (ou plus) avec la nouvelle procédure consistant uniquement à télécharger, dézipper et renommer les dossiers des modules et de leur configuration

::

  # MONITORINGS
  cd
  wget https://github.com/PnX-SI/gn_module_monitoring/archive/0.5.0.zip
  unzip 0.5.0.zip
  rm 0.5.0.zip
  mv /home/`whoami`/gn_module_monitoring /home/`whoami`/gn_module_monitoring_old
  mv /home/`whoami`/gn_module_monitoring-0.5.0 /home/`whoami`/gn_module_monitoring
  cp ~/gn_module_monitoring_old/config/conf_gn_module.toml  ~/geonature/config/monitorings_config.toml
  
  # EXPORTS
  cd
  wget https://github.com/PnX-SI/gn_module_export/archive/1.4.0.zip
  unzip 1.4.0.zip
  rm 1.4.0.zip
  mv /home/`whoami`/gn_module_export /home/`whoami`/gn_module_export_old
  mv /home/`whoami`/gn_module_export-1.4.0 /home/`whoami`/gn_module_export
  cp ~/gn_module_export_old/config/conf_gn_module.toml  ~/geonature/config/exports_config.toml
  # Le dossier de stockage des exports a été modifié de geonature/backend/static/exports/ à geonature/backend/media/exports/.
  # La configuration Apache fournie avec GeoNature 2.12 sert directement le dossier media sans passer par gunicorn.
  # Si vous aviez modifié votre configuration spécifiquement pour le module d’export, il est recommandé de retirer cette partie spécifique au profit de la configuration générique de GeoNature
  
  # DASHBOARD
  cd
  wget https://github.com/PnX-SI/gn_module_dashboard/archive/1.3.0.zip
  unzip 1.3.0.zip
  rm 1.3.0.zip
  mv /home/`whoami`/gn_module_dashboard /home/`whoami`/gn_module_dashboard_old
  mv /home/`whoami`/gn_module_dashboard-1.3.0 /home/`whoami`/gn_module_dashboard


2/ TaxHub
~~~~~~~~~

- RAS – 1.11.1 déjà installée

3/ Vue Export de la Synthèse
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- La vue gn_synthese.v_synthese_for_export définissant la structure et le contenu des exports de la Synthèse a été modifiée pour être optimisée. Si vous l'aviez customisée, reportée vos modifications manuellement après la mise à jour de GeoNature

.. IMPORTANT::

  Vérifier que je n'ai rien apporté/modifié sur cette vue

4/ GeoNature
~~~~~~~~~~~~

- Mettre à jour GeoNature

::

  cd
  wget https://github.com/PnX-SI/GeoNature/archive/2.12.2.zip
  unzip 2.12.2.zip
  rm 2.12.2.zip
  mv /home/`whoami`/geonature/ /home/`whoami`/geonature_old/
  mv GeoNature-2.12.2 /home/`whoami`/geonature/
  cd geonature

  ./install/migration/migration.sh

- Relancer les services

::

  sudo systemctl start geonature
  sudo systemctl restart geonature-worker
  sudo systemctl restart usershub
  sudo systemctl restart taxhub


- Le script ``migration.sh`` se charge de déplacer automatiquement les différents fichiers suite à la réorganisation des dossiers (customisation, médias, mobile, configuration centralisée des modules)

.. IMPORTANT::

  ERREUR 403 FORBIDEN sur GeoNature, idem sur version non test ? Du à la configuration apache qui change ? Pas réussi à résoudre sur test en télétravail

5/ Autres
~~~~~~~~~

- Il est fortement conseillé d'utiliser la configuration Apache générée par défaut dans ``/etc/apache2/conf-available/geonature.conf`` et de l'inclure dans votre vhost (``/etc/apache2/sites-available/geonature.conf`` et/ou ``/etc/apache2/sites-available/geonature-le-ssl.conf``), en suivant la `documentation dédiée <https://docs.geonature.fr/installation.html#configuration-apache>`_
- Si vous aviez customisé la page d’accueil de GeoNature en modifiant les composants ``frontend/src/custom/components/introduction/introduction.component.html`` et ``frontend/src/custom/components/footer/footer.component.html`` ceux-ci ont été supprimés au profit de paramètres de configuration. Il vous faut donc déplacer votre customisation dans les paramètres ``TITLE``, ``INTRODUCTION`` et ``FOOTER`` de la nouvelle section ``[HOME]`` de la configuration de GeoNature. Vous pouvez renseigner du code HTML sur plusieurs lignes en le plaçant entre triple quote (``"""<b>Hello</b>"""``).
- Les paramètres de configuration suivants ont été supprimés et doivent être retirés de votre fichier de configuration (``config/geonature_config.toml``) s’ils sont présents :

    - ``LOGO_STRUCTURE_FILE`` (si vous aviez renommé votre logo, déplacez le dans ``geonature/custom/images/logo_structure.png``)
    - ``UPLOAD_FOLDER`` (si vous l’aviez déplacé, renommez votre dossier d’upload en attachments et placez-le dans le dossier des médias (``geonature/backend/media/`` par défaut, paramétrable via ``MEDIA_FOLDER``))
    - ``BASE_DIR``

6/ Occtax et champs additionnels
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Les champs additionnels de type ``bool_radio`` ne sont plus supportés.
Si vous utilisiez ce type de widget dans vos champs additionnels d'Occtax, ils seront automatiquement remplacés par un widget de type ``radio``.
Vous devez changer le champs ``field_values`` sur le modèle suivant : ``[{"label": "Mon label vrai", "value": true }, {"label": "Mon label faux", "value": false }]``.
- Les champs de formulaire de type ``radio``, ``select``, ``multiselect`` et ``checkbox``, attendent désormais une liste de dictionnaire ``{value, label}`` (voir doc des champs additionnels) (#2214)
La rétrocompatibilité avec des listes simples est maintenue, mais vous êtes invités à modifier ces champs dans le backoffice.
Pour conserver le bon affichage lors de l'édition des données, renseignez l'ancienne valeur deux fois dans la clé ``value`` et la clé ``label``.


.. NOTE::

  NOTA BENE :
  - Il n'est plus nécessaire de rebuilder le frontend ni de recharger GeoNature manuellement à chaque modification de la configuration de GeoNature ou de ses modules
  - Les taches automatisées sont désormais gérées par Celery Beat et installées avec GeoNature. Si vous aviez mis en place des crons pour mettre à jour les profils de taxons (ou les données du module Dashboard, ou les exports planifiés du module Export), supprimez les (dans ``/etc/cron.d/geonature`` ou ``crontab -e``) car ils ne sont plus utiles

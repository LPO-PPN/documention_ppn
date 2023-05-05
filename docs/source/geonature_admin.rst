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

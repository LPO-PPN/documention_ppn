Installation de Mergin Maps CE Edition
=====

.. warning::

   Cette documentation est actuellement en développement/en test
   
Installer Docker et Docker-File si ce n'est pas déjà fait

.. code-block:: console

    sudo apt install docker
    sudo usermod -aG docker $USER
    sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
    sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
    sudo service docker restart
    
Suivre l'installation de Mergin (https://merginmaps.com/docs/dev/mergince/#how-to-deploy-mergin-maps-ce)

Configurer le fichier .prod.env, puis :

.. code-block:: console

    mkdir -p projects # or wherever you set it to be
    sudo chown -R  901:999 ./projects/
    sudo chmod g+s ./projects/
    sudo docker-compose -f docker-compose.yml up
    
.. note::

   Erreur Proxy retournée, installation test à pousuivre ultérieurement
   ERROR: for proxy  Cannot start service proxy: OCI runtime create failed: container_linux.go:344: starting container process caused "process_linux.go:424: container init caused \"rootfs_linux.go:58: mounting \\\"/nginx.conf\\\" to rootfs \\\"/var/lib/docker/overlay2/c157cedad0a1eef68796e42df5b3630ac9078afd14f37c85e5a9c1a5d4d3296f/merged\\\" at \\\"/var/lib/docker/overlay2/c157cedad0a1eef68796e42df5b3630ac9078afd14f37c85e5a9c1a5d4d3296f/merged/etc/nginx/conf.d/default.conf\\\" caused \\\"not a directory\\\"\"": unknown: Are you trying to mount a directory onto a file (or vice-versa)? Check if the specified host path exists and is the expected type
   ERROR: Encountered errors while bringing up the project.  

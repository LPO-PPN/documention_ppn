[TUTO] Mise à jour des niveaux de sites GeoNature
=================================================

1/ Préambule : Tables 1-n liées à la table ``l_areas``
------------------------------------------------------

Liste des tables 1-n liées à la table ``l_areas``

- ``gn_monitoring.cor_site_area`` (id_area, id_base_site) ---> MONITORINGS
- ``gn_synthese.cor_area_synthese`` (id_synthese, id_area) ---> SYNTHESE
- ``taxonomie.bdc_statut_cor_text_area`` (id_area, id_text) ---> BDC STATUTS

2/ Préambule : Triggers liées à la table ``l_areas``
----------------------------------------------------

``tri_calculate_geojson``
~~~~~~~~~~~~~~~~~~~~~~~~~

Appel : ``ref_geo.fct_tri_calculate_geojson()``
Sur qui ? : ``FOR EACH ROW EXECUTE PROCEDURE``
Quand ? : ``BEFORE INSERT OR UPDATE OF geom``

Fait quoi ? : 

.. code-block:: sql

    NEW.geojson_4326 = public.ST_asgeojson(public.st_transform(NEW.geom, 4326));

``tri_meta_dates_change_l_areas``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Appel : ``public.fct_trg_meta_dates_change()``
Sur qui ? : ``FOR EACH ROW EXECUTE PROCEDURE``
Quand ? : ``BEFORE INSERT OR UPDATE OF geom``

Fait quoi ? : 

.. code-block:: sql

    if(TG_OP = 'INSERT') THEN
                NEW.meta_create_date = NOW();
        ELSIF(TG_OP = 'UPDATE') THEN
                NEW.meta_update_date = NOW();
                if(NEW.meta_create_date IS NULL) THEN
                        NEW.meta_create_date = NOW();
                END IF;
        end IF;
        return NEW;

``tri_insert_cor_area_synthese``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Appel : ``gn_synthese.fct_trig_l_areas_insert_cor_area_synthese_on_each_statement()``
Sur qui ? : ``FOR EACH STATEMENT EXECUTE PROCEDURE``
Quand ? : ``AFTER INSERT`` 

Fait quoi ? : 

.. code-block:: sql

    -- Intersection de toutes les observations avec les nouvelles zones et écriture dans cor_area_synthese
    INSERT INTO gn_synthese.cor_area_synthese (id_area, id_synthese)
    SELECT
        new_areas.id_area AS id_area,
        s.id_synthese as id_synthese
    FROM NEW as new_areas
    join gn_synthese.synthese s
        ON public.ST_INTERSECTS(s.the_geom_local, new_areas.geom)
    WHERE new_areas.enable IS true
        AND (
                ST_GeometryType(s.the_geom_local) = 'ST_Point'
            OR
            NOT public.ST_TOUCHES(s.the_geom_local, new_areas.geom)
        );

3 / Préambule : Contrainte d'unicité
------------------------------------

Une contrainte d'unicité est à respecter sur la table ``l_areas``: la combinaison des champs ``id_type`` et ``area_code`` doit être unique !

4 / Supression des sites obsolètes
----------------------------------

.. NOTE::

    Les sites concernés par la MAJ de Mai 2023 / SEP
    > RNMO (NIV 3)
    > RNLDN (NIV 2)
    > RNBA (TOUT)
    > RNVACH (TOUT)

.. IMPORTANT::

    Avant tout, sauvegarder la base !

Supprimer les objets liés aux sites à supprimer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Executer sur psql pour gagner un peu en performance

.. code-block::

    sudo su postgres
    psql

.. code-block:: sql

    \c geonature2db

    -- Suppression des entités dans les tables de correlation
    DO $do$ DECLARE area int; BEGIN FOR area IN (SELECT id_area FROM ref_geo.l_areas WHERE source IN ('RNVACH','RNBA') UNION ALL SELECT id_area FROM ref_geo.l_areas WHERE source = 'RNMO' AND id_type = 35 UNION ALL SELECT id_area FROM ref_geo.l_areas WHERE source = 'RNLDN' AND id_type = 34) LOOP DELETE FROM gn_synthese.cor_area_synthese WHERE id_area = area; DELETE FROM gn_monitoring.cor_site_area WHERE id_area = area; DELETE FROM taxonomie.bdc_statut_cor_text_area WHERE id_area = area; DELETE FROM gn_sensitivity.cor_sensitivity_area WHERE id_area = area; END LOOP; END; $do$;

Supprimer les sites concernés
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    -- Suppression des entités dans la table l_areas
    -- ATTENTION Requête longue ! environ 5 minutes pour 86000 données
    DO $do$ DECLARE area int; BEGIN FOR area IN (SELECT id_area FROM ref_geo.l_areas WHERE source IN ('RNVACH','RNBA') UNION ALL SELECT id_area FROM ref_geo.l_areas WHERE source = 'RNMO' AND id_type = 35 UNION ALL SELECT id_area FROM ref_geo.l_areas WHERE source = 'RNLDN' AND id_type = 34) LOOP DELETE FROM ref_geo.l_areas WHERE id_area = area; END LOOP; END; $do$;

Ajouter les nouveaux sites
~~~~~~~~~~~~~~~~~~~~~~~~~~

Pour gain de performence, nous allons désactiver le trigger lourd de SYNTHESE

.. code-block:: sql

    -- Les sites auront un id_area déjà défini à partir de l'id = 760000, on redéfinira la séquence après
    -- Mettre les nouveaux sites dans une même table, puis l'importer dans le schéma gn_imports.new_ref_geo_mai_2023
    -- Désactivation du trigger pour cor_area_synthese

    ALTER TABLE ref_geo.l_areas DISABLE TRIGGER tri_insert_cor_area_synthese;

    -- Importer les sites
    INSERT INTO ref_geo.l_areas (id_area, id_type, area_name, area_code, geom, source, comment, enable) SELECT id_area, id_type, area_name, area_code, geom, source, comment, enable FROM gn_imports.new_ref_geo_mai_2023;

    -- Redéfinir la séquence de l'id_area
    do $$ declare maxid int; begin select max(id_area) from ref_geo.l_areas into maxid; execute 'ALTER SEQUENCE ref_geo.l_areas_id_area_seq RESTART with '|| maxid; end; $$ language plpgsql;


Ajouter manuellement les correlations sur cor_area_synthese et cor_site_area
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    -- Importer les correlations dans gn_imports.new_ref_geo_mai_2023_cor_synth et gn_imports.new_ref_geo_mai_2023_cor_monit (fichier de correspondance 1 par table sans geom)
    -- Fait à partir des centroides de toutes les sites (monitorings) / observations (synthèse) avec la geom polygone des nouveaux sites

    INSERT INTO gn_synthese.cor_area_synthese (id_area, id_synthese) SELECT id_area, id_synthese FROM gn_imports.new_ref_geo_mai_2023_cor_synth;
    INSERT INTO gn_monitoring.cor_site_area (id_area, id_base_site) SELECT id_area, id_base_site FROM gn_imports.new_ref_geo_mai_2023_cor_monit;

    -- Réactiver le trigger pour cor_area_synthese
    ALTER TABLE ref_geo.l_areas ENABLE TRIGGER tri_insert_cor_area_synthese;

Créer sa documentation en reStructuredText
================================

Le contenu de cette section est entièrement copié de `cette page du blog de FLOZz <https://blog.flozz.fr/2020/09/07/introduction-a-sphinx-un-outil-de-documentation-puissant/>`_, qui a décrit à la perfection la manière d'écrire du contenu en reStructuredText. N'hésitez pas à aller visiter son blog !

La syntaxe utilisée pour rédiger une documentation avec Sphinx s'appelle reStructuredText. Si vous êtes habitués au Markdown, vous verrez que cette syntaxe est beaucoup plus complète, mieux normalisée, mais aussi plus stricte.

Je pourrais vous écrire un article complet sur le reStructuredText tellement la syntaxe est complète. Mais comme ce n'est pas le sujet principal de cet article, je vous montre rapidement les principaux formatages, sans vous expliquer toutes les subtilités.

Voici quelques formatages inline : 

.. code-block:: RST

    Voici du texte en *italique*, en **gras**, et voici du ``code inline``.

Pour faire des liens, c'est aussi assez simple (notez bien l'espace avant le "<", il est très important) :

.. code-block:: RST

    Pour faire un lien inline c'est simple :
    lien vers le `blog de FLOZz <https://blog.flozz.fr/>`_

Voici comment on fait des paragraphes en reStructuredText :

.. code-block:: RST

    Ceci est un paragraphe. Je peux retourner à la ligne, je
    serais toujours dans le même paragraphe.

    Pour écrire un second paragraphe, il suffit de le séparer
    du premier par une ligne vide.

Pour organiser son contenu, il peut être utile d'utiliser des titres. En reStructuredText, il suffit de souligner une ligne pour faire un titre :

.. code-block:: RST

    Titre principal
    ===============

    Titre de niveau 2
    -----------------

    Titre de niveau 3
    ~~~~~~~~~~~~~~~~~

    Un autre titre de niveau 2
    --------------------------

Ici je vous ai mis ma façon de faire (qui est relativement répendue) mais vous pouvez utiliser pas mal de caractères différents pour souligner vos titres (=-~_#^+<>:'"...), le parseur se débrouillera pour déterminer le niveau du titre en fonction de l'ordre d'apparition des symboles ; le tout c'est de rester cohérent.

Besoin d'une liste à puce ou ordonnée ?

.. code-block:: RST

    Liste à puce :

    * Ceci est une liste
    * un autre élément
    * un dernier élément

    Liste ordonnée :

    1. Un
    2. Deux
    3. Trois

    Une autre liste ordonnée :

    #. Un
    #. Deux
    #. Trois    

Résultat : 

Liste à puce :

* Ceci est une liste
* un autre élément
* un dernier élément

Liste ordonnée :

1. Un
2. Deux
3. Trois

Une autre liste ordonnée :

#. Un
#. Deux
#. Trois   

Dans une documentation on a souvent besoin d'écrire du code :

.. code-block:: RST

    Voici comment faire un bloc que code simple ::

        Ceci est un bloc de code. Il est créé grâce aux doubles deux-points.

    On peut également placer les doubles deux-points seuls si on ne veut pas
    terminer sa phrase par ce symbole.

    ::

        Voici un autre bloc de code...

    Et c'est pas fini ! On peut aussi définir un bloc de code avec une syntaxe
    plus explicite, grâce à laquelle on peut indiquer à Sphinx dans quel
    langage il est rédigé, ce qui lui permettra d'activer la coloration
    syntaxique :

    .. code-block:: python

        #!/usr/bin/env python

        print("Ceci est un bloc de code Python\n")

Si vous voulez mettre en évidence des notes, des avertissements ou des choses importantes, c'est également possible :

.. code-block:: RST

    .. NOTE::

        Ceci est une note.

    .. WARNING::

        Ceci est un avertissement !

    .. IMPORTANT::

        Ceci est important !

Résultat :

.. NOTE::

    Ceci est une note.

.. WARNING::

    Ceci est un avertissement !

.. IMPORTANT::

    Ceci est important !

Il est également possible d'ajouter des images (après l'avoir déposée dans le dossier _static) :

.. code-block:: RST

    Voici une image :

    .. figure:: ./_static/image.png

    Voici un autre image avec quelques paramètres en plus :

    .. figure:: ./_static/image.png
        :alt: Texte alternatif
        :target: http://blog.flozz.fr/
        :width: 400px
        :align: center

        Texte affiché sous l'image

Et pour les plus fifou d'entre vous, il est également possible de faire des tableaux, avec des cellules fusionnées et tout ! Et pour faire ça, il suffit simplement de dessiner le tableau tel qu'on veut le voir s'afficher :

.. code-block:: RST

    +-----------+-----------+-----------+
    | Heading 1 | Heading 2 | Heading 3 |
    +===========+===========+===========+
    | Hello     | World     |           |
    +-----------+-----------+-----------+
    | foo       |                       |
    +-----------+          bar          |
    | baz       |                       |
    +-----------+-----------------------+

Résultat : 

+-----------+-----------+-----------+
| Heading 1 | Heading 2 | Heading 3 |
+===========+===========+===========+
| Hello     | World     |           |
+-----------+-----------+-----------+
| foo       |                       |
+-----------+          bar          |
| baz       |                       |
+-----------+-----------------------+

Sachez qu'en plus des éléments de syntaxe standards de reStructuredText, Sphinx rajoute de nombreux éléments supplémentaires pour les besoins de la documentation.

On a pu voir par exemple toctree un peu plus tôt, mais il y a également des syntaxes pour effectuer des références entre des éléments de la doc, des syntaxes pour documenter des classes, des fonctions,...

Je vous en dis pas plus pour cette fois-ci, et allez voir `la documentation de Sphinx <https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html>`_ pour en apprendre davantage. 

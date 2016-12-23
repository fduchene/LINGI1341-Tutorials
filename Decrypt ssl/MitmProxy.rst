########################################################
Déchiffrer du traffic crypté avec Wireshark et MitmProxy
########################################################

Dans ce tutoriel, nous allon voir comment mettre en place un environement propice au
déchiffrement de connexions sécurisées. Pour déchiffrer la connexion, nous devons
mettre la main sur l'une des choses suivantes :

- La clé privée du serveur distant, utilisée pour chiffer la connexion
- Le secret de session utilisé par la connexion que l'on souhaite déchiffer.

Ce tutoriel va montrer les deux façons de la faire. L'une après l'autre.

---------------------------
Installer les outils requis
---------------------------

Pour déchiffrer le traffic chiffré, il nous faudra deux outils :

.. _Wireshark: https://www.wireshark.org/
.. _MitmProxy: https://mitmproxy.org/
.. _lienWireshark : https://www.wireshark.org/#download
.. _lienMitmProxy : http://docs.mitmproxy.org/en/stable/install.html

- Wireshark_ (Installer Wireshark: lienWireshark_)
- MitmProxy_ (Installer MitmProxy: lienMitmProxy_)

-------------------------------
Logger les secrets de connexion
-------------------------------

Pour cette partie ci, nous allons définir une variable d'environnement qui va dire à nos navigateurs de logger les secrets de
connexion dans un fichier log. Cette technique a un défaut majeur: elle ne marche qu'avec les vieilles version de Firefox (< 48) 
et les vieilles version de Chrome (< ? ); cela a été désactivé dans les version suivantes pour des raisons de sécurité.  
Pour enregistrer les secret de connexion, on va utiliser les commandes suivantes.

.. code-block:: bash

    # Sous linux

    # Création dossier qui va contenir les fichiers de log
    mkdir ~/logBrowsers

    # On définit la variable d’environnement qui va dire à nos 
    navigateurs de logger les secrets de session
    export SSLKEYLOGFILE="~/logBrowsers/browserSecret.log"

    # On créé le fichier de log
    touch $SSLKEYLOGFILE

La façon de faire sous Windows est sensiblement la même (excepté que pour définir un variable d’environnement il faut passer 
par la GUI Windows et redémarrer après). Pour les mac users, c'est la même chose (je pense,  je n'ai pas de mac sous la main).

Une fois cela fait, lancer votre navigateur DANS LE MEME CONSOLE pour qu'il prenne les variables d'environnement et qu'ils output 
les secrets de session dans le fichier, lancez Wireshark et commencez l'enregistrement des paquets comme d'habitude.

A la fin de la session d'enregistrement, dans Wireshark, allez dans *Edit -> Préférences -> Protocols -> SSL* Une fois là, dans 
le champ *(Pre)-Master-Secret log filename* indiquez l'emplacement du fichier de log définit plus tôt (si vous avez tapé les 
commandes texto, cela devrait être ici : ~/logBrowsers/browserSecret.log). Cliquez sur *Appliquer*, votre traffic ssl est maintenant déchiffré.


.. figure:: optn1.png
   :scale: 200%

-----------------------------
Utiliser un proxy *indiscret*
-----------------------------

Cette technique est beaucoup plus fiable car elle ne dépend pas d'une version de navigateur et se contente plutôt d'utiliser un outil fait pour cela plutôt qu'un outil de débug.

Définir les variables d'environnement
-------------------------------------

Comme dans la partie précédente de ce tuto, on va commencer par définir la variable de session qui va indiquer à notre proxy où exporter les secrets de session. Je montrerais les commandes linux.

.. code-block:: bash

    # Sous linux

    # Création dossier qui va contenir les fichiers de log
    mkdir ~/logBrowsers

    # On définit la variable d’environnement qui va dire à nos 
    navigateurs de logger les secrets de session
    export MITMPROXY_SSLKEYLOGFILE="~/logBrowsers/proxySecret.log"

    # On créé le fichier de log
    touch $MITMPROXY_SSLKEYLOGFILE

    # On lance le proxy avec l'option anticache qui va 
    nous assurer que le browser n'utilise pas son cache local
    mitmdump --anticache

Définir le proxy
----------------

Laisser la console utilisée ouverte, vous devriez voir défiler les connexion sur le proxy.
La prochaine étape va être de dire à notre navigateur d'utiliser le proxy local et d'importer le certificat ssl custom qui a été généré.

.. _DocFirefox : https://support.mozilla.org/fr/kb/parametres-avances-firefox#w_paramaetres-de-connexion

Définir un proxy sous firefox : DocFirefox_

Pour Chrome : *Paramètre -> Proxy*

Comme paramètres de proxy, mettez comme host : 127.0.0.1 et comme port : 8080 (Si vous avez un doute sur le port, allez regarder dans le console de mitmdump, c'est indiqué au tout début)

Importer le certificat custom
-----------------------------

Pour ajouter le certificat custom, allez à l'adresse suivante : *mitm.it* et cliquez sur la plateforme adaptée. Ce certificat a été généré au lancement de mitmproxy et est unique. Votre navigateur va vous demandez si vous acceptez le certificat custom, activez toutes les option inhérente au certificat et confirmez. Vous êtes à présent prêt à capturer su traffic chiffré de votre navigateur. Lancez la capture sur deux interfaces lo et votre interface vous connectant à internet (vous aurez le traffic en double mais, en fonction de ce que vous faites, cela peut s'avérer nécessaire. De toute façon tout sera déchiffré.). Naviguez, capturez et sauvez la capture.

Déchiffrer la capture
---------------------
Une petite manipulation du certificat de mitmproxy va être nécessaire pour que Wireshark le comprenne. Tout d'abord, allez dans *~/.mitmproxy* et copiez le fichier appelé *mitmproxy-ca.pem* autre part. En ouvrant la copie du fichier dans un éditeur de texte, vous devriez voir quelque chose comme ca :

::

  -----BEGIN PRIVATE KEY-----
  MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDbToO7LQKhd2L9
  [...]
  jP70J+fhPHl3dVTssDNxbsB/ATvCo4fBCQB3YwktGFV6gK7dc1QTnod9CZRR16w8
  sRogZeBC4ycv8OeVDX9VXBeH
  -----END PRIVATE KEY-----
  -----BEGIN CERTIFICATE-----
  MIIDoTCCAomgAwIBAgIGDXfhyTD+MA0GCSqGSIb3DQEBCwUAMCgxEjAQBgNVBAMM
  [...]
  HTR9IkCrAyTcXqC4X0v/Q9TuSLEsP1TiERdEXGdlbPqfXKwAJd+Y7RIAe7FcJ5lW
  QR7spJGZcsVezti/S/uhb3KoU7CS
  -----END CERTIFICATE-----

Note:  "[...]" veut juste dire que le fichier est plus long mais qu'il a été raccourcis pour des raisons de lisibilité. Supprimez tout ce qui se trouve entre *begin certifiacte* et *end certificate* (marqueurs inclus) pour arriver à qqch comme ça :

::

  -----BEGIN PRIVATE KEY-----
  MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDbToO7LQKhd2L9
  [...]
  jP70J+fhPHl3dVTssDNxbsB/ATvCo4fBCQB3YwktGFV6gK7dc1QTnod9CZRR16w8
  sRogZeBC4ycv8OeVDX9VXBeH
  -----END PRIVATE KEY-----

Sauvez et quittez, félicitation, vous venez de faire le plus compliqué.
Dans Wireshark, allez de nouveau dans *Edit -> Préférences -> Protocols -> SSL* et mettez dans le champ *(Pre)-Master-Secret log filename* l'emplacement de la valeur d'environnement définie plus tot (si vous n'avez rien  changé aux commandes : *~/logBrowsers/proxySecret.log*).

.. figure:: optn1.png
   :scale: 200%

Cliquez sur *Edit* a coté de *RSA kay list*.

.. figure:: optn2.png
   :scale: 200%

Sur la fenêtre qui vient de s'ouvrir, cliquez sur *nouveau* . Remplissez les champs de la façon suivante:

- ip : *
- port : 443
- protocol : http
- Key file : l'emplacement du fichier que vous venez de modifier.
- password :

Cliquez sur valider trois fois. Félicitation, vous êtes devant un capture complètement déchiffrée de votre session de navigation.

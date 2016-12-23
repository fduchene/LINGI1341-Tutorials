Emulateur de trafic (Google Chrome) 
=================================== 
 
Introduction 
------------ 
| Il peut �tre int�ressant, pour un site internet, de pouvoir s'adapter au type de connexion dont l'utilisateur dispose. Gr�ce � cet outil, vous allez pouvoir tester un site � l'aide d'une vari�t� de connexions. 
| Cet outil vous permet de faire varier le d�bit de download, le d�bit d'upload et m�me le round-trip-time (RTT). 
| En effet, si la connexion est faible et que la page met trop de temps � charger, l'utilisateur risque de partir. 
| Plusieurs travaux ont prouv� qu'un site a 50ms (1/20 de seconde !) pour faire une bonne premi�re impression [#]_. 
| Si ce genre d'article vous int�resse, je vous conseille aussi l'article '*Powers of 10 : Time Scales in User Experience*' [#]_. 
 
Comment le serveur sait-il que vous avez une connexion � bas d�bit ? 
-------------------------------------------------------------------- 
| Il existe diff�rents moyens que vous pouvez mettre en place.  
| Une des techniques consiste � v�rifier combien de temps met le client pour charger une certaine ressource.  
| Certains fournisseurs ont associ� diff�rents range d'adresse IP avec un d�bit pr�cis.  
 
Le saviez-vous ? 
---------------- 
| Si vous voulez directement passer � la pratique, passez ce paragraphe ! :) 
| Pour �tre s�r que les �quipes de d�veloppement de Facebook se sentent concern�s par rapport � ce probl�me, l'entreprise propose � ses employ�s de simuler une connexion ayant un d�bit �quivalent � une connexion 2G. 
| Cette technique est appel�e le '*2G Tuesday*'. La connexion est donc limit�e � 40Ko (50Ko �tant le d�bit th�orique de la 2G). Pour davantage d'informations googler le terme ou alors rendez-vous sur :  
| http://www.numerama.com/business/128458-facebook-instaure-le-2g-tuesday-pour-se-confronter-au-bas-debit.html 
 
 
O� la trouver ? 
--------------- 
| Avant de se lancer dans le vif du sujet, il est important de noter que l'activation de cette option ne va pas modifier le comportement des autres onglets ouverts. 
| Passons d�s � pr�sent � la pratique en utilisant ladite fonctionnalit� qu'offre la navigateur Google Chrome. 
| Pour l'atteindre, commencez par ouvrir la console (F12). Ensuite, allez dans l'onglet '*Network*'. 
| A la droite de '*No throttling*' devrait se trouver une petite fl�che, celle-ci d�ploie un menu qui permet de faire varier le d�bit. Cette option s'appliquera d�s que vous changerez de page (un rafraichissement fonctionne aussi) 
.. image:: images/optionLoc.png 
	:height: 500px 
| Dans ma version de google chrome, la valeur en ms repr�sente le RTT, ensuite la seconde valeur repr�sente le d�bit de t�l�chargement enfin la derni�re valeur (souvent inf�rieure � la pr�c�dente) est le d�bit d'envoi. D�s que l'option est activ�e, une ic�ne apparait afin de vous en informer. 
.. image:: images/iconeActive.png 
	:scale: 65 % 
|  
| Bien que l'outil de google propose un ensemble limitations par d�faut, il est possible que vous vouliez tester le site sous certaines conditions bien pr�cises. Vous pouvez donc ajouter des configurations personnalis�es. Dans la premi�re image, vous remarquez l'option '*add*'. Si vous cliquez dessus, vous obtenez ceci 
.. image:: images/menuCustom.png 
	:scale: 65 % 
| Ce menu vous permet aussi de modifier et supprimer les profils d�j� cr��es. 
| Apr�s avoir cliqu� sur le bouton d'ajout, un menu se d�ploie vous permettant de sp�cifier les d�bits de download et d'upload ainsi que la latence d�sir�e. 
.. image:: images/menuCustomAdd.png 
	:scale: 65 % 
| Une fois le profil cr��e, il sera disponible dans la section '*custom*' du menu d�roulant. 
 
Et l'option disable chache, � quoi sert-elle ? 
----------------------------------------------- 
| Comme on peut le supposer, cette option va effectivement d�sactiver le cache. C'est � dire que lorsque vous chargez une page vous allez charger enti�rement la page, ce qui inclus toutes les images (m�me celles d�j� charg�es), les diff�rentes biblioth�ques, ... 
| D�sactiver cette option est tr�s utile lorsqu'on d�veloppe un site, ou qu'on le d�bug. Car elle permet de recharger les fichiers de style, les biblioth�ques li�es, ... 
 
 
Que pouvez-vous faire pour am�liorer l'exp�rience utilisateur ? 
--------------------------------------------------------------- 
| Tout d'abord la premi�re chose � effectuer est d'augmenter le Timeout de certaines requ�tes �tant donn� que celles-ci mettront plus de temps � charger. 
| Changer l'ordre des requ�tes pour que le client charge en premier les informations les plus importantes. 
| Limiter les downloads les moins important, voire les supprimer (images, police de caract�re, image de fond, ...). 
| Mettre en place un syst�me qui compresse les images, voire plus simplement mettre des images de haute qualit� pour les bonnes connexions et des images moins lourdes pour les connexions plus faibles. 
| Une fois ces diff�rents syst�mes mis en place n'oubliez pas de les tester gr�ce � l'�mulateur de trafic !  
 
Sources  
------- 
| https://developers.google.com/web/tools/chrome-devtools/network-performance/network-conditions 
| https://ma.ttias.be/simulate-low-bandwidth-conditions-with-chromes-network-throttling/ 
 
 
.. [#] http://www.anaandjelic.typepad.com/files/attention-web-designers-2.pdf 
.. [#] https://www.nngroup.com/articles/powers-of-10-time-scales-in-ux/


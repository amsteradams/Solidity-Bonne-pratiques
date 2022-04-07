# Principales attaques connues : 

## Reentrency : 

**Explications**

Prenons le cas d'un contrat qui a une fonction withdraw. La fonction verifie d'abord que le msg.sender a les fonds, ensuite il envoie l'argent puis il soustrait la balance du msg.sender.
La faille se trouve dans le fait de soustraire la balance après avoir envoyer les fonds, car quand le contrat attaquant recoit les fonds, cela trigger une fonction fallback ou receive qui va immediatement rappeler la fonction withdraw sans la laisser aller soustraire les fonds. C'est recursif donc ça continue de soustraire les fonds jusqu'à qu'il n'y ait plus d'ethers dans le contrat

**S'en prémunir :**

Il faut simplement placer la soustraction de la balance avant l'envoie des fonds.

Un autre moyen existe, il consiste à require en debut de fonction que la balance n'est pas lock (avec un mapping address => bool initialisé au départ), ensuite la lock, faire les computations, et la délock à la fin de la fonction.

## Oracle manipulation :

**Explications**

Un oracle est un service utilisé par un smart contract pour acceder à des données off chain. Plusieurs failles peuvent donc survenir : 

* L'oracle est il honnête ?
* L'oracle est il sûr ?
* La source de l'oracle est elle sûre ?

**S'en prémunir**

* Choisir un Oracle trustable
* Vérifier les informations de l'oracle quand même 
* Utiliser des plusieurs Oracles peut être une solution (pas d'oeufs dans le même panier)
Pour les Oracles qui apportent de la donnée sur des prix :
* Preferer l'utilisation de moyennes de prix sur une periode avec plusieurs sources

- Spot price manipulation : 

Cela consiste à jouer sur le prix d'un token, pour s'en prémunir : https://docs.uniswap.org/protocol/reference/periphery/libraries/OracleLibrary

- Off Chain Infrastructure : 

L'oracle pour récuperer sa donnée  et l'envoyer utilise des services off chain comme vu précèdemment, ces services doivent être parés à tout attaques, c'est une des faille d'un oracle

- Centralized Oracles and trust : 

Un oracle centralisé peut être le fait d'integrer par exemple 'à la main' une fonction dans notre SC pour récuperer de la donnée exterieure, par exemple une fonction onlyOwner, et l'owner peut envoyer des resultats de match pour une Dapplication de bet sportif. Il faut que l'owner soit honnête.

- Decentralized Oracles : 

Un oracle décentralisé peut piocher ses information d'une multitude de sources, cela rend très compliqué pour un pirate de fausser les données. Par contre il faut que le système (recompense des gens qui soumettent la données chez l'oracle par ex) soit fait d'une telle manière que les utilisateurs ne soient pas tentés de soummettre de la donnée faussée.



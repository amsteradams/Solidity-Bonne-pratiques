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
- Pour les Oracles qui apportent de la donnée sur des prix :
* Preferer l'utilisation de moyennes de prix sur une periode avec plusieurs sources

- Spot price manipulation : 

Cela consiste à jouer sur le prix d'un token, pour s'en prémunir : https://docs.uniswap.org/protocol/reference/periphery/libraries/OracleLibrary

- Off Chain Infrastructure : 

L'oracle pour récuperer sa donnée  et l'envoyer utilise des services off chain comme vu précèdemment, ces services doivent être parés à tout attaques, c'est une des faille d'un oracle

- Centralized Oracles and trust : 

Un oracle centralisé peut être le fait d'integrer par exemple 'à la main' une fonction dans notre SC pour récuperer de la donnée exterieure, par exemple une fonction onlyOwner, et l'owner peut envoyer des resultats de match pour une Dapplication de bet sportif. Il faut que l'owner soit honnête.

- Decentralized Oracles : 

Un oracle décentralisé peut piocher ses information d'une multitude de sources, cela rend très compliqué pour un pirate de fausser les données. Par contre il faut que le système (recompense des gens qui soumettent la données chez l'oracle par ex) soit fait d'une telle manière que les utilisateurs ne soient pas tentés de soummettre de la donnée faussée.

## FrontRunning : 

**Explications**

Lorsqu'une transaction est crée, elle est envoyée dans le mempool. A ce moment elle est visible par les mineurs qui peuvent decider de faire passer leurs transaction avant.

**S'en prémunir**

Pour l'éviter il faut essayer au maximum de verifier et limiter qui a le droit de lancer la fonction quand possible. Eviter aussi l'interêt de temps (qu'une transaction ait un interêt à passer avant une autre).

## TimeStamp dependance : 

**Explication**

La faille consiste pour un smart contract d'utiliser le timeStamp pour generer un nombre aleatoire, ou un nombre qui pourrait avantager une partie. Il y a un timestamp produit toutes les 15 sec (minage d'un bloc) et d'une part, le timeStamp est visible de tous, mais il peut aussi être manipulé par un mineur pour avoir le bon timeStamp

**S'en prémunir**

Préferer l'utilisation d'un oracle pour obtenir un aleatoire ou du temps.

## Insecure Arithmetic : 

Obselete depuis 0.8;

## Denial of Service : 

* DOS with require : 

**Explications**

Si par exemple  nous avons un contrat de vente aux enchere. Dans la fonction pour miser, on enregistre la plus grande enchere et on rembourse le dernier encherisseur et on require qu'il soit bien remboursé. Seulement si le dernier encherisseur est un smart contract qui n'a pas de fallback ou receive, le contrat sera bloqué et ne pourra jamais finir sa fonction mise() car le require ne passera jamais puisque l'envoi ne se fera jamais. Et personne ne pourra remiser puisque le dernier echerisseur ne pourra jamais être payé.


**S'en prémunir :**

Pour ce cas, il faudrait enregistrer dans un mapping le refund auquel chaque user a droit si sa mise est dépassé par une autre et créer une autre fonction que l'user appelera lui même pour se faire refund.

**Autre exemple**

Aussi pour un contrat auction, si le refund loop sur les biders (on require que l'envoie est bien fait à chaque tour) et qu'un des biders est un contrat qui ne possede pas de fallback, tous les refunds seront revert.

* DOS with gas Limit : 

**Explications**

Supposons que dans notre smart contract nous avons un tableau, et ce tableau peut être rempli de quelque chose par n'importe quel user sans limites. Si nous avons aussi dans une des fonctions de notre contrat une loop qui parcourt ce tableau, un utilisateur malveillant pourrait envoyer des millions de trucs dans notre tableau (puisqu'il n'y a pas de limite), et du coup notre fonction qui loop couterait + de 15 millions de gas pour louper notre gigantesque tableau et la fonction reverterait automatiquement.

**S'en prémunir :**

Ne pas laisser de tableau settables par n'importe qui sans limites, limiter la taille du tableau, limiter la loop etc...




**source : https://consensys.github.io/smart-contract-best-practices/attacks/denial-of-service/**
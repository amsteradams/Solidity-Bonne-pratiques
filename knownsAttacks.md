# Principales attaques connues : 

## Reentrency : 

** Exemple **

Prenons le cas d'un contrat qui a une fonction withdraw. La fonction verifie d'abord que le msg.sender a les fonds, ensuite il envoie l'argent puis il soustrait la balance du msg.sender.
La faille se trouve dans le fait de soustraire la balance après avoir envoyer les fonds, car quand le contrat attaquant recoit les fonds, cela trigger une fonction fallback ou receive qui va immediatement rappeler la fonction withdraw sans la laisser aller soustraire les fonds. C'est recursif donc ça continue de soustraire les fonds jusqu'à qu'il n'y ait plus d'ethers dans le contrat

** S'en prémunir **

Il faut simplement placer la soustraction de la balance avant l'envoie des fonds.
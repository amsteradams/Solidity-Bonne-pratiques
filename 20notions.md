# 20 bonnes pratiques en solidity

### 1- Utiliser les bonnes méthodes de test 
* Require pour la plupart des verifications simples
* if, else pour les verifications plus complexes et les computations
* assert utilisé dans de rares cas (librairies)

### 2- Attention à l'arondi de la division
Penser à multiplier la valeur et la traiter dans le front pour avoir un résultat de division plus précis.

### 3- Modifiers uniquement pour controller quelque chose
Le modifier doit être l'equivalent d'une fonction view, il ne doit pas changer d'etat ou faire un appel externe sous peine de reentrency attack.

### 4- Fonction fallback qui verifie qu'on ne lui envoie pas de donner
* Les simplifier au maximum
* Verifier les données envoyées au fallback (require(msg.data.length == 0))

### 5- Verrouiller le pragma à une version précise
Si c'est pas le cas, le compilateur cherche la bonne version et cela coûte du gas.

### 6- Utiliser des events pour surveiller l'activité
Il est important d'avoir un log du contrat et de savoir ce qu'il se passe sur notre dApp.

### 7- Ne pas utiliser tx.origin
Cela sert dans l'éventualité ou un user fait un appel à une fonction qui fait appel à une fonction d'un autre contrat pour récuperer le msg.sender originel.
C'est pas une bonne pratique et c'est amené à disparaitre à l'avenir, utiliser plutôt des verifs dans la fonction du contrat A ou transmettre à la fonction du contrat B le msg.sender.

### 8- Utiliser le type d'interface au lieu de l'addresse

### 9- Regroupement des variables, optimisation en gas
Les smarts contrats comportent des emplacements de 256 bits pour le stockage. Il est donc préferable de packer nos variables pour optimiser les emplacements.

* exemple : 

```
//bad 
uint 128 a;
uint 256 b;
uint 128 c;

```
```
//good 
uint 128 a;
uint 128 b;
uint 256 c;

```
Note : on peut aller de 8 en 8 pour choisir les bits de l'uint
un uint8 par exemple va de 0 à 256 
 
### 10- Regroupement des variables dans les structs

### 11- Pas besoin d'initialiser les variables à 0
Les uints sont automatiquement initialisées à zero par defaut, les ré initialiser depense du gas pour rien

### 12- Utiliser des messages d'erreur courts et concis

### 13- Eviter les contrôles répétitifs

### 14- Faire un swap en 1 ligne 
Au lieu de faire un algo inutile, solidity prévoit le swap nativement 
* exemple : 
```(balance, amount) = (amount, balance);```

### 15- Appeler les fonctions internes est moins cher

### 16- Mapping vs Array
On va avoir tendance à préferer le mapping pour son cout moins élevé sauf si il faut iterer dessus, il nous faut un tableau.

### 17- Array Fixe vs Dynamique
Préferé l'array fixe quand c'est possible, c'est moins cher (et declarable en memory si dans une fonction).

### 18- Delete
Ethereum rembourse une partie des fees si supprime avec le mot clef delete.

### 19 -Stockage de données dans les event
Valable uniquement sur les blockchain ou les fees sont élevés.
C'est une bonne pratique de sortir de la data dans des events pour le front car cela coûte moins cher.
(peut ralentir le front si on va chercher un event passsé il y a longtemps)

### 20 - Bien choisir entre storage et memory (&calldata);


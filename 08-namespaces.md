# Namespaces : Organiser votre code

## Le problème des noms identiques

Lorsque vous travaillez sur un projet PHP, surtout s'il devient grand ou si vous utilisez des bibliothèques externes, vous pouvez rapidement rencontrer un problème : **les conflits de noms**.

Imaginez que vous avez :
*   Votre propre classe `User` (pour gérer les utilisateurs de votre application).
*   Une bibliothèque externe que vous utilisez qui a aussi une classe `User` (pour gérer les utilisateurs de leur système, par exemple).

Si vous faites simplement :
```php
// Fichier : mon_code.php
require 'MaClasseUser.php';     // Votre classe
require 'lib_externe/User.php'; // La classe User de la bibliothèque

$myUser = new User(); // Quelle classe User est appelée ici ? Ambiguïté !
``` 
PHP ne saurait pas quelle classe User utiliser. Cela provoquerait une erreur ou un comportement imprévu.

Les Namespaces (espaces de noms) résolvent ce problème en permettant d'organiser les classes, interfaces, fonctions et constantes dans des "compartiments" logiques, évitant ainsi les collisions de noms.

## Qu'est-ce qu'un Namespace ?
Un Namespace est comme un dossier virtuel ou un nom de famille pour vos classes. Il permet de donner un nom complet et unique à une classe.

- MaClasseUser devient MonApplication\Utils\User
- La classe User de la bibliothèque devient MonSuperLib\Auth\User

Même si les deux s'appellent User à la fin, leur "chemin complet" ou "nom de famille" est différent, ce qui les rend uniques.

## Déclarer un Namespace
Un namespace est déclaré au tout début d'un fichier PHP (après l'ouverture de la balise <?php, avant tout autre code ou HTML).

```PHP
// Fichier : src/Vehicles/Car.php

namespace App\Vehicles; // Le namespace de cette classe

class Car {
    public string $model;

    public function __construct(string $model) {
        $this->model = $model;
    }

    public function drive(): string {
        return "La voiture {$this->model} roule.";
    }
}
```
Maintenant, pour PHP, cette classe n'est plus juste `Car`, c'est `App\Vehicles\Car`

## Utiliser des classes avec des Namespaces
Pour utiliser une classe qui se trouve dans un namespace, vous avez deux options :
## 1. Utiliser le nom complet (Fully Qualified Name)
Vous pouvez toujours utiliser le nom complet de la classe, y compris son namespace.

```PHP
// Fichier : index.php

require 'src/Vehicles/Car.php'; // On inclut le fichier (ou on utilise l'autoloading)

$myCar = new App\Vehicles\Car("Clio"); // Utilisation du nom complet
echo $myCar->drive() . PHP_EOL;

// Imaginez une autre classe dans un autre namespace
// $anotherCar = new OtherLib\Automobiles\Car("Ford");
```
Cette méthode est claire mais peut devenir longue à écrire.

## 2. Importer avec le mot-clé `use`
C'est la méthode la plus courante et la plus propre. Le mot-clé `use vous permet de "déclarer" que vous allez utiliser une classe d'un certain namespace, et ensuite de l'appeler par son nom court.

```PHP
// Fichier : index.php

require 'src/Vehicles/Car.php'; // Ou via l'autoloading (recommandé !)

use App\Vehicles\Car; // On "importe" la classe Car du namespace App\Vehicles

$myCar = new Car("Clio"); // Maintenant, on peut juste écrire 'Car'
echo $myCar->drive() . PHP_EOL;

// Si vous avez deux classes 'User' différentes :
// use MonApplication\Users\User;
// use ExternalLib\Auth\User as ExternalUser; // On peut même lui donner un alias !

// $appUser = new User();
// $libUser = new ExternalUser();
```
Le `use rend votre code beaucoup plus lisible.

## Namespaces et Autoloading
Les Namespaces sont intrinsèquement liés à l'Autoloading, en particulier avec le standard PSR-4 et l'outil Composer.
- **PSR-4** : Recommande que le chemin des dossiers de vos fichiers PHP reflète leur structure de namespace. Par exemple, la classe App\Vehicles\Car devrait se trouver dans un fichier MonProjet/src/App/Vehicles/Car.php.
- **Composer** : C'est Composer qui lit cette structure (définie dans composer.json) et configure l'autoloading pour vous. Ainsi, lorsque vous faites use App\Vehicles\Car;, Composer s'assure que le fichier src/App/Vehicles/Car.php est automatiquement chargé quand Car est utilisée pour la première fois.

## En bref
- Les Namespaces résolvent les conflits de noms entre les classes.
- Ils organisent votre code de manière logique et hiérarchique.
- On les déclare avec namespace Ma\MonNamespace; au début du fichier.
- On les utilise avec leur nom complet ou, plus couramment, avec le mot-clé `use` pour des noms courts.
- Ils sont fondamentaux pour le développement PHP moderne, surtout avec Composer.
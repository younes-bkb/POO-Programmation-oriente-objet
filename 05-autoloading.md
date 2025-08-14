# Autoloading : Charger vos classes automatiquement

## Le problème des inclusions manuelles

Jusqu'à présent, pour utiliser nos classes (`Vehicle`, `Car`, `Truck`), nous aurions dû les inclure manuellement au début de chaque fichier PHP qui les utilise :

```php
// index.php
require 'Vehicle.php'; // Si Vehicle est dans un fichier Vehicle.php
require 'Car.php';     // Si Car est dans un fichier Car.php

$myCar = new Car("Clio", 5);
echo $myCar->start();
```
Imaginez si votre projet contient des dizaines, voire des centaines de classes !

- Fastidieux : Il faudrait taper require pour chaque fichier.
- Source d'erreurs : Oublier une inclusion ou se tromper dans le chemin est fréquent.
- Performance : Inclure des fichiers dont les classes ne sont pas utilisées immédiatement est inutile.

L'autoloading résout ce problème.

## Qu'est-ce que l'Autoloading ?
L'autoloading (ou "chargement automatique") est un mécanisme qui permet à PHP de charger automatiquement une classe (c'est-à-dire le fichier où elle est définie) uniquement lorsqu'elle est utilisée pour la première fois.

Au lieu de faire require 'MaClasse.php' vous faites simplement new MaClasse();. Si PHP ne connaît pas encore MaClasse, il va demander à un "chargeur automatique" où trouver le fichier correspondant.

### Comment ça marche ?
- Vous tentez d'utiliser une classe (new Car()). 
- PHP voit que la classe Car n'est pas encore définie.
- PHP vérifie si des fonctions d'autoloading ont été enregistrées.
- Si oui, PHP appelle ces fonctions, leur passant le nom de la classe recherchée (Car).
- La fonction d'autoloading doit alors trouver le fichier de la classe (Car.php par exemple) et l'inclure avec require ou include.
- Une fois le fichier inclus, la classe est définie et PHP peut continuer son exécution.

### Mettre en place l'Autoloading avec spl_autoload_register()
La fonction spl_autoload_register() est la manière moderne et recommandée de mettre en place l'autoloading en PHP. Elle permet d'enregistrer une ou plusieurs fonctions (ou méthodes de classe) qui seront appelées quand une classe inconnue est rencontrée.

```PHP
// Dans un fichier central (par exemple, 'autoload.php' ou 'index.php')

// Ceci est notre "chargeur automatique"
function customAutoloader(string $className) {
    // Convertir le nom de la classe en un chemin de fichier.
    // Par exemple, 'Car' devient 'Car.php'
    // 'App\Models\Vehicle' pourrait devenir 'App/Models/Vehicle.php'
    $filePath = $className . '.php';

    // Vérifier si le fichier existe et l'inclure
    if (file_exists($filePath)) {
        require $filePath;
    }
}

// Enregistre notre fonction comme un chargeur automatique
spl_autoload_register('customAutoloader');

// --- Maintenant, on peut utiliser nos classes sans les "require" manuellement ---

// Imaginez que Car.php contient :
// class Car { public function __construct($model) { echo "Car {$model} créée."; } }

// et Vehicle.php contient :
// class Vehicle { public function __construct($model) { echo "Vehicle {$model} créée."; } }

$myCar = new Car("Clio"); // PHP va appeler customAutoloader('Car')
echo PHP_EOL;
$myVehicle = new Vehicle("Moto"); // PHP va appeler customAutoloader('Vehicle')
echo PHP_EOL;

// Sortie :
// Car Clio créée.
// Vehicle Moto créée.
```

Dans cet exemple simple, nous supposons que le nom du fichier est exactement le même que le nom de la classe, avec l'extension .php

## L'Autoloading moderne : PSR-4 et Composer
Dans les projets PHP professionnels, l'autoloading est géré de manière standardisée grâce aux PSR (PHP Standard Recommendations), notamment le **PSR-4**

Le PSR-4 définit une façon standard d'organiser vos classes dans des dossiers en utilisant les namespaces (sujet à venir dans une prochaine fiche !)

- Namespace = chemin de dossier
- Nom de la classe = nom du fichier

Par exemple, une classe `App\Models\Car` serait stockée dans votre`_projet/src/App/Models/Car.php`

Heureusement, vous n'avez généralement pas besoin d'écrire votre propre fonction spl_autoload_register() complexe. L'outil de gestion de dépendances le plus populaire en PHP, **Composer**, fait tout le travail pour vous.

Lorsque vous utilisez Composer et définissez vos namespaces dans votre fichier composer.json, Composer génère automatiquement un fichier vendor/autoload.php. Il vous suffit d'inclure ce seul fichier au début de votre application :

```PHP
// C'est la seule ligne "require" dont vous avez besoin dans un projet Composer
require __DIR__ . '/vendor/autoload.php';

// Maintenant, toutes vos classes sont automatiquement chargées quand vous les utilisez
use App\Models\Car;
use App\Models\Vehicle;

$myCar = new Car("Clio", 5);
// ...
````

Composer utilise spl_autoload_register() en arrière-plan avec des règles PSR-4 efficaces.

L'autoloading est une fonctionnalité essentielle qui rend la gestion des classes dans les grands projets PHP beaucoup plus simple, propre et performante.
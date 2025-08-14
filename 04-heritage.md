# L'Héritage en POO : Réutiliser et étendre le code

## Pourquoi l'héritage ?

Dans le monde réel, un concept général (comme `Vehicle`) peut avoir des sous-catégories plus spécifiques (comme `Car` ou `Truck`). Ces sous-catégories partagent les caractéristiques et les comportements du concept général, mais y ajoutent aussi leurs propres spécificités.

Si nous devions créer des classes `Car` et `Truck` séparément, nous répéterions beaucoup de code commun (propriétés comme la marque, le modèle ; méthodes comme démarrer, arrêter). L'héritage nous permet d'éviter cette répétition.

L'héritage, c'est quand une classe (la "fille") prend tout ce qu'une autre classe (la "mère") possède, et y ajoute ou modifie des choses.

## Le concept de l'Héritage

Une nouvelle classe (la **classe fille** ou **sous-classe**) peut hériter des propriétés et des méthodes d'une classe existante (la **classe mère** ou **super-classe**). En PHP, on utilise le mot-clé `extends`.

```php
// La classe mère (ou super-classe)
class Vehicle {
    protected string $model;

    public function __construct(string $model) {
        $this->model = $model;
    }

    public function start(): string {
        return "Le {$this->model} démarre.";
    }

    public function getModel(): string {
        return $this->model;
    }
}

// La classe fille (ou sous-classe)
class Car extends Vehicle { // 'Car' est un 'Vehicle'
    private int $numberOfDoors;

    public function __construct(string $model, int $numberOfDoors) {
        // Appelle le constructeur de la classe mère (Vehicle)
        parent::__construct($model);
        $this->numberOfDoors = $numberOfDoors;
    }

    public function getNumberOfDoors(): int {
        return $this->numberOfDoors;
    }

    // Nouvelle méthode spécifique à la voiture
    public function openTrunk(): string {
        return "Le coffre de la {$this->model} s'ouvre.";
    }
}

// Utilisation
$myCar = new Car("Clio", 5);
echo $myCar->start() . PHP_EOL;           // Méthode héritée de Vehicle
echo $myCar->getModel() . PHP_EOL;        // Méthode héritée de Vehicle
echo $myCar->openTrunk() . PHP_EOL;       // Méthode spécifique à Car
echo $myCar->getNumberOfDoors() . PHP_EOL; // Méthode spécifique à Car
```
Dans cet exemple, `Car` hérite de `Vehicle`. Elle utilise les méthodes start() et getModel() de Vehicle et ajoute sa propre propriété ($numberOfDoors) et sa méthode (openTrunk()).

## Visibilité et Héritage
La visibilité des propriétés et méthodes (public, protected, private) a un impact sur l'héritage :
- public : Les membres public d'une classe mère sont toujours accessibles dans les classes filles et depuis l'extérieur.
- protected : Les membres protected d'une classe mère sont accessibles dans la classe mère elle-même et dans toutes ses classes filles. Ils ne sont pas accessibles de l'extérieur. C'est idéal pour partager des éléments au sein de la "famille" de classes.
- private : Les membres private sont accessibles uniquement dans la classe où ils sont définis. Une classe fille ne peut pas accéder directement aux membres private de sa classe mère.

## Surcharge de méthodes (Method Overriding)
Une classe fille peut surcharger (override) une méthode héritée. Cela signifie qu'elle redéfinit une méthode qui a le même nom que dans la classe mère, pour lui donner un comportement différent spécifique à la classe fille.

```PHP
class ElectricCar extends Car { // 'ElectricCar' est un type de 'Car'
    public function __construct(string $model, int $numberOfDoors) {
        parent::__construct($model, $numberOfDoors);
    }

    // Surcharge de la méthode start() héritée de 'Vehicle' (via 'Car')
    public function start(): string {
        return "La {$this->getModel()} démarre silencieusement (électrique).";
    }
}

$myElectricCar = new ElectricCar("Tesla Model 3", 4);
echo $myElectricCar->start() . PHP_EOL;      // Affiche la version surchargée par ElectricCar
echo $myElectricCar->openTrunk() . PHP_EOL;  // Méthode héritée de Car
```

Ici, ElectricCar a sa propre façon de "démarrer" qui est différente d'une voiture normale.
## Le mot-clé final
Le mot-clé final empêche l'héritage ou la surcharge.
final class : Empêche une classe d'être héritée.

```PHP
final class HighSecurityCar {
    public function lockDoors(): string {
        return "Portes verrouillées très fort.";
    }
}
// class ArmoredCar extends HighSecurityCar {} // ❌ Erreur : HighSecurityCar est finale.
```
final public function : Empêche une méthode d'être surchargée par une classe fille.

```PHP
class BaseVehicle {
    final public function getSerialNumber(): string {
        return "SN12345"; // Ce numéro de série ne doit pas changer
    }
}

class SportsCar extends BaseVehicle {
    // public function getSerialNumber(): string {} // ❌ Erreur : impossible de surcharger une méthode finale.
}
```

## Avantages de l'Héritage
- Réutilisabilité du code : Moins de code dupliqué puisque les éléments communs sont hérités.
- Organisation : Crée une hiérarchie logique qui reflète les relations entre les concepts.
- Facilité de maintenance : Les changements dans la classe mère se propagent aux classes filles (sauf surcharge).
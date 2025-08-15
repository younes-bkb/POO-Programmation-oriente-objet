# Classes Abstraites et Interfaces : Des plans et des contrats

## Classes Abstraites : Des plans incomplets

Imaginez l'idée générale d'un `Vehicle` 
 Vous ne conduisez pas un "véhicule générique", mais une `Car`  ou une `Motorcycle` 

Une **classe abstraite** est :
*   Un plan pour d'autres classes, mais qui n'est **pas complet**.
*   Elle peut avoir des méthodes normales (avec leur code).
*   Mais elle a aussi des **méthodes abstraites** : ce sont des méthodes déclarées, mais sans code. Elles n'ont pas de `{}`.
*   Une classe abstraite **ne peut pas être utilisée directement** (`new Vehicle()` est impossible).
*   Toute classe qui hérite d'une classe abstraite (**classe fille**) doit **obligatoirement** écrire le code de toutes les méthodes abstraites.

C'est utile pour forcer les classes filles à avoir certaines fonctions, tout en partageant d'autres éléments.

```php
// Notre classe abstraite
abstract class Vehicle {
    protected string $model;

    public function __construct(string $model) {
        $this->model = $model;
    }

    // Méthode abstraite : toutes les classes filles DOIVENT définir comment on "roule"
    abstract public function drive(): string;

    // Méthode normale (avec son code)
    public function start(): string {
        return "Le {$this->model} démarre.";
    }
}

// Une classe qui est un type de Vehicle
class Car extends Vehicle {
    // On DOIT écrire le code de la méthode drive()
    public function drive(): string {
        return "La voiture {$this->model} roule sur la route.";
    }
}

// Un autre type de Vehicle
class Motorcycle extends Vehicle {
    // On DOIT écrire le code de la méthode drive()
    public function drive(): string {
        return "La moto {$this->model} roule sur deux roues.";
    }
}

// Utilisation
// $genericVehicle = new Vehicle("Concept"); // ❌ Erreur : Impossible d'instancier une classe abstraite

$myCar = new Car("Clio");
echo $myCar->start() . PHP_EOL;  // Méthode normale héritée
echo $myCar->drive() . PHP_EOL;  // Méthode abstraite implémentée

$myMotorcycle = new Motorcycle("Harley");
echo $myMotorcycle->start() . PHP_EOL;
echo $myMotorcycle->drive() . PHP_EOL;
```

## Interfaces : Définir des contrats de comportement
Une interface est un contrat. Elle dit : "Toute classe qui utilise cette interface doit savoir faire ceci, cela et ça."

- Elle contient uniquement des déclarations de méthodes, sans aucun code.
- Elle ne contient aucune propriété.
- Une classe qui `implements` une interface doit obligatoirement écrire le code de toutes les méthodes de cette interface.
- Une classe peut implémenter plusieurs interfaces.

C'est parfait pour garantir que des classes différentes (qui n'ont pas forcément de lien d'héritage) peuvent toutes "faire la même chose" d'une manière spécifique.

```PHP
// Notre interface : le contrat "peut s'arrêter"
interface StoppableInterface {
    public function stop(): string;
}

// Une classe qui respecte le contrat Stoppable
class Car implements StoppableInterface {
    private string $model;

    public function __construct(string $model) {
        $this->model = $model;
    }

    // Obligation d'écrire le code de la méthode stop()
    public function stop(): string {
        return "La voiture {$this->model} s'arrête.";
    }
}

// Une autre classe qui respecte le même contrat
class Train implements StoppableInterface {
    private string $name;

    public function __construct(string $name) {
        $this->name = $name;
    }

    // Obligation d'écrire le code de la méthode stop()
    public function stop(): string {
        return "Le train {$this->name} freine et s'arrête en gare.";
    }
}

// Utilisation
$myCar = new Car("Peugeot");
echo $myCar->stop() . PHP_EOL;

$myTrain = new Train("TGV");
echo $myTrain->stop()
```

Ici, StoppableInterface est un contrat. Car et Train sont très différents, mais en implémentant StoppableInterface, nous sommes sûrs qu'ils ont tous les deux une méthode stop().

## En bref
- **Classes Abstraites** : Des plans incomplets pour les classes filles, forçant l'implémentation de certaines méthodes. On ne peut pas en créer d'objet directement.
- **Interfaces** : Des contrats de comportement. Toute classe qui les utilise doit implémenter toutes leurs méthodes.
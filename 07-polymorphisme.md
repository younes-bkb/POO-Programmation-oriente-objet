# Polymorphisme : Agir différemment sur des objets similaires

## Qu'est-ce que le Polymorphisme ?

Le mot "polymorphisme" vient du grec et signifie "plusieurs formes". En POO, le polymorphisme, c'est la capacité pour des objets de types différents de répondre à un même appel de méthode de manière spécifique à leur propre type.

Imaginez que vous avez une télécommande universelle pour plusieurs types de véhicules :
*   Appuyez sur "Démarrer" : Une voiture démarre avec le moteur, une voiture électrique démarre silencieusement, un vélo démarre en pédalant.
*   Le bouton est le même, mais l'action sous-jacente est différente pour chaque véhicule.

Le polymorphisme vous permet d'écrire du code qui peut interagir avec des objets sans avoir à connaître leur type exact, tant qu'ils partagent une "forme" ou un "contrat" commun (via l'héritage ou une interface).

## Comment ça marche ? (Via héritage et interfaces)

Le polymorphisme s'appuie sur ce que nous avons vu précédemment :
*   **L'Héritage :** Une classe fille peut surcharger (redéfinir) des méthodes de sa classe mère.
*   **Les Classes Abstraites et Interfaces :** Elles définissent un "contrat" (méthodes à implémenter) que les classes concrètes doivent respecter.

### Exemple simple avec héritage (et surcharge)

Reprenons nos véhicules avec une méthode `honk()` (klaxonner) :

```php
class Vehicle {
    protected string $model;

    public function __construct(string $model) {
        $this->model = $model;
    }

    public function honk(): string {
        return "Le {$this->model} klaxonne : Bip bip !";
    }
}

class Car extends Vehicle {
    // La voiture klaxonne comme un véhicule classique
}

class Truck extends Vehicle {
    // Le camion surcharge la méthode honk() pour son propre son
    public function honk(): string {
        return "Le camion {$this->model} klaxonne : Klaxoonnn !";
    }
}

class Bicycle extends Vehicle {
    // Le vélo surcharge la méthode honk() pour son propre son
    public function honk(): string {
        return "Le vélo {$this->model} fait : Ding ding !";
    }
}

// Utilisation polymorphique : une liste de véhicules différents
$vehicles = [
    new Car("Clio"),
    new Truck("Volvo"),
    new Bicycle("VTT")
];

foreach ($vehicles as $vehicle) {
    echo $vehicle->honk() . PHP_EOL; // Chaque objet réagit différemment à honk()
}

// Sortie :
// Le Clio klaxonne : Bip bip !
// Le camion Volvo klaxonne : Klaxoonnn !
// Le vélo VTT fait : Ding ding !
```

Ici, même si $vehicle est toujours traité comme un Vehicle dans la boucle foreach, la méthode `honk()` appelée est celle qui est spécifique à l'objet réel (`Car`, `Truck`, ou `Bicycle`).

## Exemple simple avec interface
Utilisons l'interface StoppableInterface de la fiche précédente :

```PHP
interface StoppableInterface {
    public function stop(): string;
}

class Car implements StoppableInterface {
    private string $model;
    public function __construct(string $model) { $this->model = $model; }
    public function stop(): string {
        return "La voiture {$this->model} s'arrête en douceur.";
    }
}

class Train implements StoppableInterface {
    private string $name;
    public function __construct(string $name) { $this->name = $name; }
    public function stop(): string {
        return "Le train {$this->name} s'arrête avec un sifflement.";
    }
}

class Bicycle implements StoppableInterface {
    private string $brand;
    public function __construct(string $brand) { $this->brand = $brand; }
    public function stop(): string {
        return "Le vélo {$this->brand} s'arrête en dérapant.";
    }
}

// Une fonction qui accepte n'importe quel objet qui implémente StoppableInterface
function makeItStop(StoppableInterface $item) {
    echo $item->stop() . PHP_EOL;
}

$myCar = new Car("Peugeot");
$myTrain = new Train("TGV");
$myBicycle = new Bicycle("BMX");

makeItStop($myCar);    // La fonction makeItStop ne sait pas si c'est une voiture, mais elle sait qu'elle PEUT s'arrêter
makeItStop($myTrain);
makeItStop($myBicycle);

// Sortie :
// La voiture Peugeot s'arrête en douceur.
// Le train TGV s'arrête avec un sifflement.
// Le vélo BMX s'arrête en dérapant.
```

Ici, la fonction `makeItStop()` n'a pas besoin de savoir si elle reçoit une `Car`, un `Train` ou un `Bicycle`. Elle sait juste que l'objet qu'elle reçoit est StoppableInterface, et elle peut appeler la méthode `stop()` sur lui. Chaque objet se comporte alors de sa propre manière.

## Avantages du Polymorphisme
- Flexibilité : Permet d'écrire du code plus générique qui fonctionne avec n'importe quel objet respectant un certain contrat (interface ou classe mère).
- Extensibilité : Vous pouvez ajouter de nouveaux types d'objets (nouvelles classes filles ou classes implémentant une interface) sans avoir à modifier le code existant qui utilise le polymorphisme.
- Lisibilité et Maintenabilité : Le code est plus propre car il évite de multiples if/else ou switch pour gérer les différents types d'objets.

Le polymorphisme est un concept clé pour créer des applications POO robustes, évolutives et faciles à gérer.

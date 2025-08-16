
# Composition : Préférer "Avoir un" à "Être un"

## Le problème : les limites de l'héritage

L'héritage (`IS-A`, "est un") est un outil puissant : une `Car` **est un** `Vehicle`. Mais si on abuse de l'héritage pour tout et n'importe quoi, on peut créer des hiérarchies de classes très rigides et illogiques.

Par exemple, une voiture a un moteur. Devrions-nous faire une classe `Car` qui hérite d'une classe `Engine` ?
Non, car une voiture **n'est pas un** moteur, elle **a un** moteur.

Tenter de forcer une relation "IS-A" là où une relation "HAS-A" ("a un") est plus logique mène à du code difficile à maintenir et à faire évoluer. C'est ici que la **composition** brille.

## Qu'est-ce que la Composition ?

La composition est une technique de conception où une classe (un objet complexe) est **composée** d'autres objets (des objets plus simples), au lieu d'hériter de leurs fonctionnalités.

L'objet "conteneur" (la voiture) ne fait pas le travail lui-même ; il **délègue** les tâches aux objets qu'il "contient" (le moteur).

- **Héritage** : Relation **"IS-A"** (est un).
- **Composition** : Relation **"HAS-A"** (a un).

### Exemple : une voiture "a un" moteur

Au lieu de mettre toute la logique du moteur dans la classe `Car`, nous allons créer une classe `Engine` dédiée. La classe `Car` va ensuite **utiliser** un objet `Engine`.

```php
// Fichier : components/Engine.php

// Un objet simple et spécialisé
class Engine {
    private bool $isRunning = false;

    public function start(): string {
        if ($this->isRunning) {
            return "Le moteur tourne déjà.";
        }
        $this->isRunning = true;
        return "Moteur démarré. Vroum !";
    }

    public function stop(): string {
        $this->isRunning = false;
        return "Moteur arrêté.";
    }

    public function isRunning(): bool {
        return $this->isRunning;
    }
}

// Fichier : classes/Car.php

class Car {
    private string $model;
    // La voiture "a un" moteur. La propriété $engine contient un objet Engine.
    private Engine $engine;

    public function __construct(string $model) {
        $this->model = $model;
        // La voiture crée sa propre instance de Moteur.
        $this->engine = new Engine();
    }

    // La voiture DÉLÈGUE la tâche de "démarrer" à son moteur
    public function start(): string {
        echo "Voiture {$this->model} : ";
        return $this->engine->start();
    }

    // Elle DÉLÈGUE aussi la tâche de "s'arrêter"
    public function stop(): string {
        echo "Voiture {$this->model} : ";
        return $this->engine->stop();
    }

    public function getStatus(): string {
        $status = $this->engine->isRunning() ? "en marche" : "à l'arrêt";
        return "Le moteur de la {$this->model} est {$status}.";
    }
}

// Utilisation
$myCar = new Car("Renault Clio");
echo $myCar->start() . PHP_EOL;
echo $myCar->getStatus() . PHP_EOL;
echo $myCar->stop() . PHP_EOL;
echo $myCar->getStatus() . PHP_EOL;
```

**Résultat :**
```
Voiture Renault Clio : Moteur démarré. Vroum !
Le moteur de la Renault Clio est en marche.
Voiture Renault Clio : Moteur arrêté.
Le moteur de la Renault Clio est à l'arrêt.
```

## Les Avantages de la Composition

Cet exemple simple montre déjà plusieurs avantages :

1.  **Flexibilité** : Et si on veut créer une `ElectricCar` ? On peut lui donner un `ElectricEngine` au lieu d'un `Engine` classique, sans changer la classe `Car` (surtout si les deux moteurs respectent une `EngineInterface` !).
2.  **Code plus simple et spécialisé** : La classe `Engine` ne se préoccupe que de la logique du moteur. La classe `Car` ne se préoccupe que de la logique de la voiture. Chacune a une seule responsabilité.
3.  **Réutilisabilité** : La classe `Engine` peut être réutilisée dans d'autres classes (`Motorcycle`, `Boat`, etc.) sans aucun lien d'héritage.
4.  **Tests facilités** : On peut tester la classe `Car` en lui donnant un "faux" moteur (un *mock*), pour l'isoler et ne tester que son propre comportement.

La règle d'or du design orienté objet est : **"Préférez la composition à l'héritage"**.
Commencez par la composition, et n'utilisez l'héritage que lorsque la relation "IS-A" est évidente et logique.

## En bref


- **Héritage**  Une classe **est une** version spécialisée d'une autre (`Car extends Vehicle`).  Pour modéliser une hiérarchie stricte et partager un type commun. 
- **Composition** Une classe **a un** ou plusieurs autres objets pour faire son travail (`Car` a un `Engine`).  Pour construire des objets complexes à partir de briques plus simples et réutilisables. C'est le choix par défaut. 

La composition est l'un des principes les plus fondamentaux pour créer des applications robustes, flexibles et faciles à maintenir en programmation orientée objet.
# Traits : Partager du code horizontalement

## Le problème : la réutilisation de code sans héritage

Nous avons vu que l'héritage (`extends`) est très puissant, mais il a une limite majeure en PHP : **une classe ne peut hériter que d'une seule autre classe**.

Alors, comment faire si on veut qu'une `Car` et une `Boat` partagent la même fonctionnalité de "système GPS", sans qu'elles aient un parent commun qui possède cette logique ?
- On ne peut pas faire hériter `Car` de `Vehicle` ET de `GpsSystem`.
- On pourrait copier-coller le code du GPS dans les deux classes, mais ce serait une mauvaise pratique (duplication de code).

C'est exactement le problème que les **traits** viennent résoudre. Ils permettent de partager des méthodes (et des propriétés) entre différentes classes qui n'ont pas forcément de lien d'héritage.

## Qu'est-ce qu'un Trait ?

Un Trait, c'est un regroupement de méthodes que vous pouvez "importer" dans une classe. C'est un peu comme un "copier-coller" de code que PHP gère pour vous.

- C'est un mécanisme de **réutilisation de code horizontal**.
- Une classe peut utiliser **plusieurs traits** en même temps.
- Ce n'est pas de l'héritage. Une `Car` qui utilise un trait `Gps` ne "est pas un" `Gps`, elle "a la capacité" d'utiliser un GPS.

### Déclarer et utiliser un Trait

On déclare un trait avec le mot-clé `trait`, et on l'intègre dans une classe avec le mot-clé `use`.

```php
// Fichier : traits/Loggable.php

// Ce trait fournit une capacité de "journalisation"
trait Loggable {
    public function log(string $message): string {
        // Dans une vraie application, on écrirait dans un fichier de log
        $className = self::class;
        $logEntry = "[" . date("Y-m-d H:i:s") . "] ({$className}): {$message}";
        echo $logEntry . PHP_EOL;
        return $logEntry;
    }
}

// Fichier : classes/Car.php
class Car {
    // On importe la fonctionnalité du trait Loggable
    use Loggable;

    private string $model;

    public function __construct(string $model) {
        $this->model = $model;
        $this->log("Création d'une nouvelle voiture : {$this->model}.");
    }

    public function start() {
        $this->log("Démarrage de la {$this->model}.");
    }
}

// Fichier : classes/Database.php
// Une classe qui n'a RIEN à voir avec une voiture
class Database {
    // Mais qui peut aussi avoir besoin de journaliser des événements
    use Loggable;

    public function connect() {
        $this->log("Connexion à la base de données réussie.");
    }
}

// Utilisation
$myCar = new Car("Peugeot 308");
$myCar->start();

$db = new Database();
$db->connect();
```
Résultat :

```Code
[2023-10-27 10:30:00] (Car): Création d'une nouvelle voiture : Peugeot 308.
[2023-10-27 10:30:00] (Car): Démarrage de la Peugeot 308.
[2023-10-27 10:30:00] (Database): Connexion à la base de données réussie.
```
Comme vous le voyez, `Car` et `Database` partagent la méthode `log() sans avoir de lien de parenté, grâce au trait Loggable.

## Gestion des conflits

Que se passe-t-il si une classe utilise deux traits qui, par malchance, ont une méthode avec le même nom ? PHP lèvera une erreur, mais il nous donne les outils pour résoudre ce conflit avec les mots-clés insteadof ("à la place de") et as ("en tant que").


```PHP
trait EngineTrait {
    public function powerOn(): string {
        return "Le moteur s'allume.";
    }
}

trait RadioTrait {
    public function powerOn(): string {
        return "La radio s'allume.";
    }
}

class Car {
    // Conflit ! Les deux traits ont une méthode powerOn()
    use EngineTrait, RadioTrait {
        // On dit à PHP : "Pour powerOn(), utilise celle de EngineTrait À LA PLACE DE celle de RadioTrait"
        EngineTrait::powerOn insteadof RadioTrait;

        // Et si on veut quand même utiliser la méthode de RadioTrait ?
        // On lui donne un nouveau nom (un alias)
        RadioTrait::powerOn as turnOnRadio;
    }
}

$myCar = new Car();
echo $myCar->powerOn() . PHP_EOL;      // Appelle la méthode de EngineTrait
echo $myCar->turnOnRadio() . PHP_EOL; // Appelle la méthode de RadioTrait via son alias
```
Résultat :

```Code
Le moteur s'allume.
La radio s'allume.
``` 
## En bref

- **Trait**	Un ensemble de méthodes réutilisables pour les classes.
- **use**	Le mot-clé pour inclure un ou plusieurs traits dans une classe.
- **Utilité**	Résout la limitation de l'héritage unique et évite la duplication de code.
- **Conflits**	Gérés avec `insteadof` pour choisir une méthode et `as` pour créer un alias.

Les traits sont un outil extrêmement puissant pour écrire du code modulaire et propre en PHP, en se concentrant sur les comportements plutôt que sur la seule hiérarchie des classes.
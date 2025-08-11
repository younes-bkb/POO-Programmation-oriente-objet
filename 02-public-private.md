# Visibilité des propriétés et méthodes (public, private, protected)

## Pourquoi contrôler la visibilité ?

Imaginez votre voiture : vous utilisez le volant, les pédales et le levier de vitesse. Ce sont les éléments "publics" que vous manipulez directement.
Mais vous n'avez pas besoin de toucher au moteur, à l'injection ou aux circuits électriques internes pour conduire ; ces éléments sont "privés", cachés, essentiels au bon fonctionnement, mais pas accessibles à vous.

En programmation, la visibilité fonctionne pareil :
Certaines parties d'un objet sont comme le volant : elles sont publiques et accessibles à tous pour interagir avec l'objet.
D'autres sont comme le moteur : elles sont privées, cachées, et ne servent qu'au fonctionnement interne de l'objet.
Et parfois, il y a des éléments "protégés" qui sont un peu comme des secrets de famille, partagés uniquement avec les versions "améliorées" de la voiture (les "descendants" de l'objet).

## Les trois niveaux de visibilité

### Public : accessible partout

Le modificateur `public` rend une propriété ou une méthode accessible depuis n'importe où dans le code.

```php
class Vehicle {
    public string $brand;           // Accessible de partout
    
    public function honk(): string {
        return "Votre {$this->brand} klaxonne !";
    }
}

// Utilisation
$car = new Vehicle();
$car->brand = "Toyota";     // ✓ Fonctionne
echo $car->honk();          // ✓ Fonctionne

// Mais on peut mettre n'importe quoi !
$car->brand = "";           // ✓ Fonctionne, mais pose problème
```

**Le problème** : avec `public`, n'importe qui peut modifier nos données, même de façon incohérente.

### Private : accessible uniquement dans la classe

Le modificateur `private` rend une propriété ou une méthode accessible uniquement depuis l'intérieur de la même classe.

```php
class Vehicle {
    private string $brand;
    private int $kilometers;
    
    public function __construct(string $brand) {
        $this->brand = $brand;
        $this->kilometers = 0;
    }
    
    public function drive(int $distance): string {
        $this->kilometers += $distance;
        return "{$this->brand} a roulé {$distance} km";
    }
    
    public function getKilometers(): int {
        return $this->kilometers;
    }
    
    // Méthode privée : logique interne
    private function checkEngine(): bool {
        return true; // Vérification interne du moteur
    }
}

// Utilisation
$car = new Vehicle("Toyota");
echo $car->drive(50);           // ✓ Fonctionne
echo $car->getKilometers();     // ✓ Fonctionne : 50

// echo $car->brand;            // ❌ Erreur : propriété privée
// $car->kilometers = -100;     // ❌ Erreur : propriété privée
// $car->checkEngine();         // ❌ Erreur : méthode privée
```

**L'avantage** : impossible de mettre des données incohérentes. Toute modification passe par des méthodes contrôlées.

### Protected : accessible dans la classe et ses héritiers

Le modificateur `protected` est accessible dans la classe elle-même et dans toutes les classes qui en héritent.

```php
class Vehicle {
    protected string $brand;
    protected int $kilometers;
    
    public function __construct(string $brand) {
        $this->brand = $brand;
        $this->kilometers = 0;
    }
    
    protected function getBaseInfo(): string {
        return "{$this->brand} - {$this->kilometers} km";
    }
}

class Car extends Vehicle {
    private int $doors;
    
    public function __construct(string $brand, int $doors) {
        parent::__construct($brand);
        $this->doors = $doors;
    }
    
    public function getInfo(): string {
        // ✓ Peut accéder aux propriétés protected du parent
        return $this->getBaseInfo() . " - {$this->doors} portes";
    }
    
    public function addKilometers(int $distance): void {
        // ✓ Peut modifier les propriétés protected du parent
        $this->kilometers += $distance;
    }
}

// Utilisation
$car = new Car("Toyota", 5);
echo $car->getInfo();           // ✓ Fonctionne : "Toyota - 0 km - 5 portes"
$car->addKilometers(100);

// echo $car->brand;            // ❌ Erreur : protected pas accessible de l'extérieur
```

## Comparaison rapide

```php
class Vehicle {
    public string $owner;           // Accessible partout
    protected string $brand;        // Accessible dans la classe + héritiers
    private string $engine;         // Accessible seulement dans cette classe
    
    public function start() {       // Méthode publique
        return $this->checkEngine() ? "Démarré" : "Panne";
    }
    
    protected function getBrand() { // Méthode pour héritiers
        return $this->brand;
    }
    
    private function checkEngine() { // Méthode interne
        return $this->engine !== "";
    }
}
```

## Bonnes pratiques

### Règle d'or : tout en private par défaut

```php
class Vehicle {
    private string $brand;          // Private par défaut
    private int $kilometers;        // Private par défaut
    
    public function __construct(string $brand) {
        $this->brand = $brand;
        $this->kilometers = 0;
    }
    
    // Seules les méthodes nécessaires sont publiques
    public function getBrand(): string {
        return $this->brand;
    }
    
    public function drive(int $distance): bool {
        if ($distance < 0) return false;
        
        $this->kilometers += $distance;
        return true;
    }
}
```

## Récapitulatif

| Visibilité | Accessible depuis | Utilisation |
|------------|-------------------|-------------|
| `public` | Partout | Interface publique (méthodes d'action) |
| `protected` | Classe + héritiers | Propriétés/méthodes partagées |
| `private` | Classe uniquement | Détails internes |

La visibilité, c'est un peu comme fabriquer un appareil où l'on ne montre que les boutons que l'utilisateur peut presser (ce qu'il est autorisé à faire). Tout le mécanisme interne et les fils (comment ça marche à l'intérieur) sont cachés.
Cette séparation rend les objets plus solides et fiables, car personne ne peut toucher par erreur aux éléments cachés qui assurent leur bon fonctionnement.
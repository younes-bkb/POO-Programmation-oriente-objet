# Classes et Objets en PHP

## Pourquoi utiliser les objets ?

En PHP, on manipule principalement des types scalaires comme les nombres, les chaînes de caractères, les booléens, ainsi que des types composés comme les tableaux.
Ces types sont suffisants pour des scripts simples, mais dès qu’un projet gagne en complexité, ils montrent vite leurs limites.

Prenons l’exemple d’un site qui doit gérer des informations sur des véhicules. Avec une approche procédurale classique, on se retrouve rapidement avec une multitude de variables éparpillées et des fonctions surchargées de paramètres.
Résultat : le code devient difficile à maintenir et à faire évoluer.

C’est là qu’interviennent les objets : ils permettent de regrouper, au même endroit, les données (propriétés) et les comportements (méthodes) liés à un même concept, comme un véhicule.

## Évolution du code procédural vers l'objet

### Approche procédurale (à éviter pour des données complexes)
```php

// Variables éparpillées
$vehicleBrand = "Toyota";
$vehicleModel = "Yaris";
$vehicleYear = 2023;
$vehicleKilometers = 15000;
$vehicleIsRunning = false;

// Fonctions surchargées de paramètres
function startVehicle($brand, $model, $year, &$isRunning) {
    $isRunning = true;
    return "Le $brand $model de $year démarre";
}

function addKilometers($brand, $model, &$kilometers, $distance) {
    $kilometers += $distance;
    return "Le $brand $model a maintenant $kilometers km";
}

function stopVehicle($brand, $model, $year, &$isRunning) {
    $isRunning = false;
    return "Le $brand $model de $year s'arrête";
}

// Utilisation fastidieuse
echo startVehicle($vehicleBrand, $vehicleModel, $vehicleYear, $vehicleIsRunning);
echo addKilometers($vehicleBrand, $vehicleModel, $vehicleKilometers, 150);
echo stopVehicle($vehicleBrand, $vehicleModel, $vehicleYear, $vehicleIsRunning);
```
Le problème saute aux yeux :

Les mêmes informations (marque, modèle, année) sont répétées partout.

Chaque fonction attend une longue liste de paramètres, ce qui rend le code lourd à lire et propice aux erreurs.

Si quelqu’un d’autre reprend ce code, il devra tout reconstituer pour comprendre comment les données sont liées.

Avec les objets, toutes ces informations et comportements peuvent être regroupés dans une seule structure claire et réutilisable.

### Approche orientée objet (solution recommandée)
```php

class Vehicle {
    private string $brand;
    private string $model;
    private int $year;
    private int $kilometers;
    private bool $isRunning;
    
    public function __construct(string $brand, string $model, int $year) {
        $this->brand = $brand;
        $this->model = $model;
        $this->year = $year;
        $this->kilometers = 0;
        $this->isRunning = false;
    }
    
    public function start(): string {
        $this->isRunning = true;
        return "Le {$this->brand} {$this->model} de {$this->year} démarre";
    }
    
    public function drive(int $distance): string {
        if (!$this->isRunning) {
            return "Impossible de rouler : le véhicule n'est pas démarré";
        }
        
        $this->kilometers += $distance;
        return "Le véhicule a roulé {$distance} km. Total : {$this->kilometers} km";
    }
    
    public function stop(): string {
        $this->isRunning = false;
        return "Le {$this->brand} {$this->model} s'arrête";
    }
    
    public function getInfo(): string {
        $status = $this->isRunning ? "en marche" : "à l'arrêt";
        return "{$this->brand} {$this->model} ({$this->year}) - {$this->kilometers} km - {$status}";
    }
}

// Utilisation claire et concise
$myVehicle = new Vehicle("Toyota", "Corolla", 2023);
echo $myVehicle->start();
echo $myVehicle->drive(150);
echo $myVehicle->getInfo();
echo $myVehicle->stop();
```
Pourquoi c’est mieux ?

Toutes les données liées au véhicule (marque, modèle, année, kilomètres, état) sont regroupées dans une seule entité.

Les comportements (démarrer, rouler, arrêter, afficher les infos) sont intégrés directement à l’objet.

Plus besoin de répéter les mêmes paramètres dans chaque appel de fonction.

Le code est plus lisible, plus facile à maintenir et moins sujet aux erreurs.

---

### Classes vs Objets

Bien faire la distinction :

**Une classe**, c'est comme un plan d'architecte. Elle définit comment quelque chose doit être construit, mais ce n'est pas la chose elle-même.

```php

class Vehicle {
    // Ceci est la définition, le plan
    private string $brand;
    private string $model;
    
    public function __construct(string $brand, string $model) {
        $this->brand = $brand;
        $this->model = $model;
    }
    
    public function honk(): string {
        return "Le {$this->brand} {$this->model} klaxonne !";
    }
}
```

**Un objet** (ou instance), c'est la construction réelle basée sur ce plan :

```php
// Création d'objets concrets
$vehicle1 = new Vehicle("Renault", "Clio");
$vehicle2 = new Vehicle("BMW", "Serie 3");

// Chaque objet est indépendant
echo $vehicle1->honk(); // "Le Renault Clio klaxonne !"
echo $vehicle2->honk(); // "Le BMW Serie 3 klaxonne !"
```

### Propriétés

Les propriétés, ce sont les caractéristiques de mon objet. Pour un véhicule, ça peut être sa marque, son modèle, son kilométrage, etc.

```php
class Vehicle {
    private string $brand;        // Marque du véhicule
    private string $model;        // Modèle
    private int $year;            // Année
    private int $kilometers;      // Kilométrage
    private float $fuelLevel;     // Niveau de carburant (en %)
    private bool $isRunning;      // Véhicule démarré ou pas
    
    public function __construct(string $brand, string $model, int $year) {
        $this->brand = $brand;
        $this->model = $model;
        $this->year = $year;
        $this->kilometers = 0;
        $this->fuelLevel = 100.0;
        $this->isRunning = false;
    }
    
    // Getter pour accéder aux propriétés
    public function getBrand(): string {
        return $this->brand;
    }
    
    public function getKilometers(): int {
        return $this->kilometers;
    }
    
    public function getFuelLevel(): float {
        return $this->fuelLevel;
    }
}
```

### Méthodes

Les méthodes, ce sont les actions que mon objet peut faire. Pour un véhicule : démarrer, s'arrêter, rouler, faire le plein, etc.

```php
class Vehicle {
    private string $brand;
    private string $model;
    private int $kilometers;
    private float $fuelLevel;
    private bool $isRunning;
    
    public function __construct(string $brand, string $model) {
        $this->brand = $brand;
        $this->model = $model;
        $this->kilometers = 0;
        $this->fuelLevel = 100.0;
        $this->isRunning = false;
    }
    
    public function start(): bool {
        if ($this->fuelLevel <= 0) {
            return false;
        }
        
        $this->isRunning = true;
        return true;
    }
    
    public function drive(int $distance): bool {
        if (!$this->isRunning) {
            return false;
        }
        
        // Consommation : 1% de carburant pour 20 km
        $fuelNeeded = $distance / 20;
        
        if ($this->fuelLevel < $fuelNeeded) {
            return false;
        }
        
        $this->kilometers += $distance;
        $this->fuelLevel -= $fuelNeeded;
        
        return true;
    }
    
    public function refuel(): void {
        $this->fuelLevel = 100.0;
    }
    
    public function stop(): void {
        $this->isRunning = false;
    }
    
    public function getStatus(): array {
        return [
            'vehicle' => $this->brand . ' ' . $this->model,
            'kilometers' => $this->kilometers,
            'fuel' => round($this->fuelLevel, 1) . '%',
            'running' => $this->isRunning
        ];
    }
}
```

---

## Avantages de la POO

### Organisation du code

Avec les objets, tout ce qui concerne un véhicule est regroupé au même endroit. Les données (marque, modèle, kilométrage) et les actions (démarrer, rouler, s'arrêter) sont dans la même classe. C'est logique et facile à retrouver.

### Réutilisabilité

Une fois que j'ai créé ma classe Vehicle, je peux créer autant de véhicules différents que je veux :

```php
$car = new Vehicle("Peugeot", "308");
$truck = new Vehicle("Mercedes", "Actros");
$motorcycle = new Vehicle("Honda", "CBR");

// Chacun a son propre état
$car->drive(100);
$truck->drive(500);
// Le kilométrage de la voiture n'affecte pas celui du camion
```

### Maintenance facilitée

Si je veux changer la façon dont fonctionne le calcul de consommation, je n'est qu'à modifier la méthode `drive()` dans la classe, et tous mes véhicules utilisent automatiquement la nouvelle logique.

---


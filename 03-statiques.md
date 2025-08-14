# Propriétés et méthodes statiques

## Qu'est-ce que "statique" en POO ?

Jusqu'à présent, nous avons vu que chaque objet (ou "instance") d'une classe possède ses propres propriétés et peut exécuter ses propres méthodes. Par exemple, ma voiture "Toyota Yaris" a son propre kilométrage, et votre voiture "Renault Clio" a le sien. Chaque voiture est indépendante.

Mais parfois, nous avons besoin de propriétés ou de méthodes qui ne sont **pas liées à un objet spécifique**, mais plutôt à la **classe elle-même**. C'est là qu'interviennent les membres statiques.

Imaginez une usine de voitures :
*   Chaque voiture qui sort de l'usine est un **objet indépendant** (avec sa marque, son modèle, ses kilomètres).
*   Mais l'usine a aussi des informations qui concernent **toutes les voitures produites** ou l'usine elle-même, et non une voiture particulière : par exemple, le nombre total de voitures produites par cette usine, ou une fonction pour changer la politique de production de l'usine.

Les membres statiques (propriétés et méthodes) sont exactement cela : ils appartiennent à la **classe** et sont **partagés par toutes ses instances**, ou peuvent être utilisés sans même créer une instance.

## Propriétés statiques : des données partagées

Une propriété statique est une variable qui appartient à la classe et non à un objet spécifique. Sa valeur est **partagée par toutes les instances** de cette classe. Si une instance modifie cette propriété statique, la modification est visible par toutes les autres instances, car elles accèdent toutes à la même variable.

### Déclaration et accès
Pour déclarer une propriété statique, on utilise le mot-clé `static` :

```php
class Vehicle {
    public static int $totalVehicles = 0; // Propriété statique (appartient à la classe)
    public string $model;                 // Propriété d'instance (appartient à l'objet)

    public function __construct(string $model) {
        $this->model = $model;
        self::$totalVehicles++; // Incrémente le compteur à chaque nouvelle instance
    }
}

// Accès à la propriété statique via la classe
echo "Véhicules avant création : " . Vehicle::$totalVehicles . PHP_EOL; // Affiche 0

$car1 = new Vehicle("Clio");
echo "Véhicules après Clio : " . Vehicle::$totalVehicles . PHP_EOL; // Affiche 1

$car2 = new Vehicle("Yaris");
echo "Véhicules après Yaris : " . Vehicle::$totalVehicles . PHP_EOL; // Affiche 2

echo $car1->model . PHP_EOL; // Affiche "Clio" (propriété d'instance, spécifique à $car1)
// echo $car1::$totalVehicles; // ⚠️ Possible, mais préférez toujours ClassName::$propertyName pour la clarté.

```

**Analogie** : $totalVehicles est comme le grand tableau d'affichage dans l'usine qui compte le nombre total de voitures fabriquées. Ce compteur est unique pour toute l'usine, peu importe quelle voiture est en train d'être assemblée.

## Méthodes statiques : des actions liées à la classe

Une méthode statique est une fonction qui appartient à la classe et peut être appelée sans avoir besoin de créer une instance de cette classe. Elle est utile pour des opérations qui n'ont pas besoin des données spécifiques d'un objet particulier.

### Déclaration et accès

Pour déclarer une méthode statique, on utilise le mot-clé static :

```PHP
class Vehicle {
    public static function getFactoryLocation(): string {
        return "Usine principale : Toulouse"; // Exemple de donnée générique pour la classe
    }

    public static function formatKilometers(int $km): string {
        return number_format($km, 0, ',', ' ') . ' km';
    }
}

// Appel d'une méthode statique via la classe
echo Vehicle::getFactoryLocation() . PHP_EOL; // Affiche "Usine principale : Toulouse"
echo "Distance parcourue : " . Vehicle::formatKilometers(123456) . PHP_EOL; // Affiche "123 456 km"

// Vous n'avez pas besoin de créer un objet Vehicle pour utiliser ces méthodes.
// $myCar = new Vehicle("SUV");
// echo $myCar->getFactoryLocation(); // ⚠️ Possible, mais préférez toujours ClassName::methodName() pour la clarté.
```

**Analogie** : getFactoryLocation() ou formatKilometers() sont comme des outils ou des informations de référence pour toute l'usine, ou pour le concept de "véhicule" en général. Ils ne dépendent pas d'une voiture spécifique.

L'absence de $this dans les méthodes statiques
Une règle très importante : dans une méthode statique, vous ne pouvez pas utiliser le mot-clé $this. 

Pourquoi ? Parce que $this fait référence à l'objet actuel, à l'instance. Or, une méthode statique est appelée sur la classe, sans qu'il y ait forcément d'objet derrière.

```PHP
class Vehicle {
    public string $model = "Modèle Inconnu"; // Propriété d'instance

    public static function displayStaticInfo(): string {
        // return $this->model; // ❌ Erreur fatale ! Impossible d'utiliser $this ici.
        return "Cette méthode affiche des informations générales sur la classe Vehicle.";
    }
}

echo Vehicle::displayStaticInfo() . PHP_EOL;
```
Si une méthode statique a besoin d'accéder à une propriété statique de la même classe, elle doit utiliser self:: (ou static:: pour des cas d'héritage plus avancés, mais self:: est suffisant pour commencer) au lieu de $this.

```PHP
class Vehicle {
    public static int $globalProductionLimit = 10000;

    public static function getProductionStatus(): string {
        return "La limite de production globale est de : " . self::$globalProductionLimit . " véhicules.";
    }
}

echo Vehicle::getProductionStatus() . PHP_EOL; // Affiche "La limite de production globale est de : 10000 véhicules."
```

## Quand utiliser les membres statiques ?
Les membres statiques sont particulièrement utiles pour :
- Compteurs d'instances : Garder une trace du nombre d'objets créés pour une classe (comme notre $totalVehicles).
- Méthodes utilitaires / Fonctions d'aide : Des fonctions qui sont liées à la classe mais qui n'ont pas besoin de l'état d'un objet pour fonctionner (ex: formater des données, des opérations mathématiques spécifiques à un domaine).
- Constantes de classe : Bien que const soit plus courant pour les constantes non modifiables, les propriétés statiques peuvent aussi servir à stocker des configurations partagées ou des états modifiables à l'échelle de la classe.
- Factory Methods (Méthodes de fabrique) : Des méthodes statiques qui construisent et retournent des instances de la classe, souvent avec une logique de création spécifique.

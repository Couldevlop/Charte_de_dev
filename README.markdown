# Charte de Bonnes Pratiques pour un Code Propre

Cette charte définit les bonnes pratiques pour écrire un code **lisible**, **maintenable** et **évolutif**, en s'inspirant du livre *Clean Code* de Robert C. Martin, de ses autres travaux (*The Clean Coder*, *Clean Architecture*), et des contributions de développeurs chevronnés comme Eric Evans, Martin Fowler, Kent Beck, et Grady Booch. Elle s'adresse aux développeurs juniors et seniors, avec des explications détaillées et des exemples concrets en **Java**. L'objectif est d'améliorer la qualité du code et de favoriser la collaboration au sein de l'équipe.

---

## Table des Matières

 1. Noms Significatifs
 2. Fonctions Petites et Focalisées
 3. Commentaires Utiles
 4. Formatage Clair
 5. Gestion des Erreurs
 6. Séparation des Concepts
 7. Tests Automatisés
 8. Refactorisation Continue
 9. Gestion des Dépendances
10. Éviter les Odeurs de Code
11. Discipline Professionnelle
12. Séparation des Préoccupations
13. Conception Pilotée par les Tests (TDD)
14. Simplicité et Minimalisme
15. Modélisation Orientée Domaine
16. Utilisation Judicieuse des Patterns de Conception
17. Documentation Vivante
18. Principe de Cohérence d’Équipe
19. Standardisation et Outils pour un Code Cohérent
20. Conclusion

---

## 1. Noms Significatifs

Les noms doivent révéler l'intention et être explicites pour réduire les ambiguïtés.

### Règles

- Utilisez des noms **descriptifs** qui indiquent le rôle de la variable, méthode ou classe.
- Évitez les **abréviations obscures** (ex. : `customer` au lieu de `cust`).
- Suivez des **conventions cohérentes** : méthodes en verbes (ex. : `calculateTotal`).
- Évitez les noms **génériques** comme `data` ou `list`.

### Exemple

**Mauvais :**

```java
int d; // durée en jours
List<String> lst;
```

**Bon :**

```java
int durationInDays;
List<String> customerNames;
```

**Exemple de méthode :**

```java
// Mauvais
public double calc(List<Double> nums) {
    double s = 0;
    for (Double n : nums) s += n;
    return s;
}

// Bon
public double calculateSum(List<Double> numbers) {
    double sum = 0;
    for (Double number : numbers) {
        sum += number;
    }
    return sum;
}
```

---

## 2. Fonctions Petites et Focalisées

Les fonctions doivent être courtes, avoir une seule responsabilité et respecter un niveau d'abstraction unique.

### Règles

- **Limitez la taille** : Maximum 20 lignes par fonction.
- **Une seule responsabilité** : Une fonction doit accomplir une seule tâche.
- **Niveau d'abstraction unique** : Ne mélangez pas la logique métier et les détails techniques.
- **Minimisez les arguments** : Préférez 0 à 2 arguments, ou utilisez un objet de configuration.

### Exemple

**Mauvais :**

```java
public void processOrder(Order order) {
    // Valider
    if (order.getItems().isEmpty()) {
        throw new IllegalArgumentException("Order cannot be empty");
    }
    // Calculer le total
    double total = 0;
    for (Item item : order.getItems()) {
        total += item.getPrice();
    }
    // Sauvegarder
    database.save(order);
}
```

**Bon :**

```java
public void processOrder(Order order) {
    validateOrder(order);
    double total = calculateOrderTotal(order);
    saveOrder(order);
}

private void validateOrder(Order order) {
    if (order.getItems().isEmpty()) {
        throw new IllegalArgumentException("Order cannot be empty");
    }
}

private double calculateOrderTotal(Order order) {
    return order.getItems().stream()
                .mapToDouble(Item::getPrice)
                .sum();
}

private void saveOrder(Order order) {
    database.save(order);
}
```

---

## 3. Commentaires Utiles

Les commentaires doivent apporter une valeur ajoutée et expliquer *pourquoi* une décision a été prise.

### Règles

- Évitez les commentaires **redondants** qui répètent le code.
- Expliquez l'**intention** ou les contraintes (ex. : raisons d'un contournement).
- Utilisez des commentaires pour les **TODOs** ou **avertissements**.
- **Maintenez les commentaires à jour** pour éviter les informations obsolètes.

### Exemple

**Mauvais :**

```java
// Incrémente i
i++;
```

**Bon :**

```java
// Contourne une limitation de l'API externe qui nécessite une tentative supplémentaire
retryCount++;
```

---

## 4. Formatage Clair

Un code bien formaté améliore la lisibilité et reflète le professionnalisme.

### Règles

- Adoptez une **convention cohérente** (ex. : Google Java Style Guide).
- Limitez la **largeur des lignes** à 120 caractères.
- Utilisez des **espaces logiques** pour séparer les blocs de code.
- **Indentez correctement** pour refléter la structure du code.

### Exemple

**Mauvais :**

```java
public class OrderService{public void process(Order order){if(order!=null){for(Item item:order.getItems()){System.out.println(item.getPrice());}}}}
```

**Bon :**

```java
public class OrderService {
    public void process(Order order) {
        if (order != null) {
            for (Item item : order.getItems()) {
                System.out.println(item.getPrice());
            }
        }
    }
}
```

---

## 5. Gestion des Erreurs

La gestion des erreurs doit être robuste et ne pas obscurcir la logique métier.

### Règles

- Utilisez des **exceptions** plutôt que des codes de retour.
- Créez des **exceptions personnalisées** pour les cas métier.
- **Ne retournez pas null** : Préférez `Optional` ou des collections vides.
- **Documentez les exceptions** dans la Javadoc.

### Exemple

**Mauvais :**

```java
public Customer findCustomer(int id) {
    if (database.contains(id)) {
        return database.get(id);
    }
    return null;
}
```

**Bon :**

```java
/**
 * Récupère un client par son ID.
 * @throws CustomerNotFoundException si le client n'existe pas
 */
public Optional<Customer> findCustomer(int id) throws CustomerNotFoundException {
    return Optional.ofNullable(database.get(id));
}
```

---

## 6. Séparation des Concepts

Les classes doivent être petites, cohérentes et respecter le principe de responsabilité unique (SRP).

### Règles

- **Une seule responsabilité** : Une classe ne doit changer que pour une seule raison.
- **Cohésion élevée** : Les méthodes et champs doivent être fortement liés.
- **Encapsulation** : Protégez les données internes avec des accesseurs/mutateurs.
- **Préférez la composition** à l’héritage pour plus de flexibilité.

### Exemple

**Mauvais :**

```java
public class Order {
    private List<Item> items;
    public void addItem(Item item) { items.add(item); }
    public double calculateTotal() { return items.stream().mapToDouble(Item::getPrice).sum(); }
    public void saveToDatabase() { database.save(this); }
}
```

**Bon :**

```java
public class Order {
    private List<Item> items;
    
    public void addItem(Item item) {
        items.add(item);
    }
    
    public List<Item> getItems() {
        return Collections.unmodifiableList(items);
    }
}

public class OrderCalculator {
    public double calculateTotal(Order order) {
        return order.getItems().stream()
                    .mapToDouble(Item::getPrice)
                    .sum();
    }
}

public class OrderRepository {
    public void save(Order order) {
        database.save(order);
    }
}
```

---

## 7. Tests Automatisés

Les tests unitaires garantissent la qualité et facilitent la refactorisation.

### Règles

- Suivez **FIRST** : Tests rapides (Fast), indépendants (Independent), répétables (Repeatable), auto-validants (Self-Validating), opportuns (Timely).
- **Un test par comportement** : Testez une seule fonctionnalité à la fois.
- **Nommez clairement** : Utilisez `should_When_` (ex. : `shouldThrowException_WhenOrderIsEmpty`).
- Visez une **couverture élevée** (80-90% pour les parties critiques).

### Exemple

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class OrderCalculatorTest {
    @Test
    void shouldCalculateTotal_WhenOrderHasItems() {
        Order order = new Order();
        order.addItem(new Item(10.0));
        order.addItem(new Item(20.0));
        OrderCalculator calculator = new OrderCalculator();
        
        double total = calculator.calculateTotal(order);
        
        assertEquals(30.0, total, 0.01);
    }
    
    @Test
    void shouldReturnZero_WhenOrderIsEmpty() {
        Order order = new Order();
        OrderCalculator calculator = new OrderCalculator();
        
        double total = calculator.calculateTotal(order);
        
        assertEquals(0.0, total, 0.01);
    }
}
```

---

## 8. Refactorisation Continue

Un code propre émerge par une refactorisation régulière pour éliminer la dette technique.

### Règles

- **Refactorisez souvent** : Améliorez le code à chaque occasion.
- Suivez la **règle du Boy Scout** : Laissez le code plus propre que vous ne l’avez trouvé.
- Utilisez des **outils de l’IDE** pour refactoriser en sécurité.
- **Testez avant et après** pour garantir l’intégrité.

### Exemple

**Avant :**

```java
public void process(Order o) {
    double t = 0;
    for (Item i : o.getItems()) {
        t += i.getPrice();
    }
    if (t > 100) {
        System.out.println("Discount applied");
        t *= 0.9;
    }
    database.save(o);
}
```

**Après :**

```java
public void processOrder(Order order) {
    double total = calculateOrderTotal(order);
    total = applyDiscountIfEligible(total);
    saveOrder(order);
}

private double calculateOrderTotal(Order order) {
    return order.getItems().stream()
                .mapToDouble(Item::getPrice)
                .sum();
}

private double applyDiscountIfEligible(double total) {
    if (total > 100) {
        System.out.println("Discount applied");
        return total * 0.9;
    }
    return total;
}

private void saveOrder(Order order) {
    database.save(order);
}
```

---

## 9. Gestion des Dépendances

Les dépendances doivent être gérées pour minimiser le couplage et maximiser la testabilité.

### Règles

- Utilisez l’**injection de dépendances** (ex. : Spring).
- Dépendez d’**interfaces**, pas de classes concrètes.
- **Évitez les singletons statiques** pour faciliter les tests.
- **Minimisez les dépendances externes** pour réduire les risques.

### Exemple

**Mauvais :**

```java
public class OrderService {
    private Database db = Database.getInstance();
    
    public void save(Order order) {
        db.save(order);
    }
}
```

**Bon :**

```java
public interface OrderRepository {
    void save(Order order);
}

public class OrderService {
    private final OrderRepository repository;
    
    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }
    
    public void save(Order order) {
        repository.save(order);
    }
}
```

---

## 10. Éviter les Odeurs de Code

Les "odeurs de code" indiquent des problèmes potentiels à corriger.

### Règles

- **Évitez les méthodes trop longues** : Décomposez-les.
- **Supprimez le code mort** : Code commenté ou inutilisé.
- **Évitez les duplications** : Factorisez le code commun.
- **Limitez les conditions imbriquées** : Utilisez des garde-fous ou des patrons comme Strategy.

### Exemple

**Mauvais :**

```java
public void process(Order order) {
    if (order != null) {
        if (!order.getItems().isEmpty()) {
            double total = 0;
            for (Item item : order.getItems()) {
                total += item.getPrice();
            }
            if (total > 0) {
                database.save(order);
            }
        }
    }
}
```

**Bon :**

```java
public void process(Order order) {
    if (order == null || order.getItems().isEmpty()) {
        return;
    }
    double total = calculateOrderTotal(order);
    if (total > 0) {
        saveOrder(order);
    }
}
```

---

## 11. Discipline Professionnelle

Dans *The Clean Coder*, Robert C. Martin insiste sur le professionnalisme, qui inclut la responsabilité personnelle et l’engagement envers la qualité du code.

### Règles

- **Assumez la responsabilité** : Ne blâmez pas les outils ou les délais pour un code de mauvaise qualité.
- **Respectez les délais** : Estimez réalistement et communiquez proactivement en cas de retard.
- **Apprenez continuellement** : Maintenez vos compétences à jour.
- **Évitez le "ça marche"** : Un code fonctionnel mais mal écrit est une dette technique.

### Exemple

**Mauvais (manque de discipline)** : Pousser un code non testé en production sous prétexte que "ça fonctionne sur ma machine".

**Bon (discipline professionnelle) :**

```java
public class PaymentProcessor {
    private final PaymentGateway gateway;
    
    public PaymentProcessor(PaymentGateway gateway) {
        this.gateway = gateway;
    }
    
    /**
     * Traite un paiement.
     * @throws PaymentException si le paiement échoue
     */
    public void processPayment(Payment payment) throws PaymentException {
        validatePayment(payment);
        gateway.execute(payment);
    }
    
    private void validatePayment(Payment payment) {
        if (payment.getAmount() <= 0) {
            throw new IllegalArgumentException("Payment amount must be positive");
        }
    }
}
```

---

## 12. Séparation des Préoccupations

Dans *Clean Architecture*, Robert C. Martin met l’accent sur la séparation des préoccupations, où chaque module ou couche a une responsabilité claire.

### Règles

- **Isolez les couches** : Séparez la logique métier, l’accès aux données et l’interface utilisateur.
- **Utilisez des abstractions** : Les couches doivent communiquer via des interfaces.
- **Évitez les fuites d’implémentation** : Une couche ne doit pas connaître les détails d’une autre.
- **Appliquez le principe de dépendance inversée** : Les modules de haut niveau ne doivent pas dépendre des détails de bas niveau.

### Exemple

**Mauvais (mélange des préoccupations) :**

```java
public class OrderController {
    private Database db = new Database();
    
    public void createOrder(HttpRequest request) {
        Order order = parseRequest(request);
        db.save(order);
        System.out.println("Order saved");
    }
}
```

**Bon (séparation des préoccupations) :**

```java
public interface OrderRepository {
    void save(Order order);
}

public class OrderService {
    private final OrderRepository repository;
    
    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }
    
    public void createOrder(Order order) {
        validateOrder(order);
        repository.save(order);
    }
    
    private void validateOrder(Order order) {
        if (order.getItems().isEmpty()) {
            throw new IllegalArgumentException("Order cannot be empty");
        }
    }
}

public class OrderController {
    private final OrderService service;
    
    public OrderController(OrderService service) {
        this.service = service;
    }
    
    public void createOrder(HttpRequest request) {
        Order order = parseRequest(request);
        service.createOrder(order);
        respondWithSuccess();
    }
}
```

---

## 13. Conception Pilotée par les Tests (TDD)

Dans *The Clean Coder*, Robert C. Martin promeut la **Test-Driven Development (TDD)** pour produire un code robuste et bien conçu.

### Règles

- **Écrivez les tests d’abord** : Chaque fonctionnalité commence par un test échouant.
- **Suivez le cycle TDD** : Rouge (test échoue), Vert (test passe), Refactoriser.
- **Gardez les tests simples** : Un test doit être facile à comprendre et rapide à exécuter.
- **Utilisez des mocks** pour isoler les dépendances externes.

### Exemple

**Cycle TDD pour une fonctionnalité de calcul de remise :**

1. **Test échouant :**

```java
@Test
void shouldApplyDiscount_WhenTotalExceedsThreshold() {
    Order order = new Order();
    order.addItem(new Item(150.0));
    OrderCalculator calculator = new OrderCalculator();
    
    double total = calculator.calculateTotalWithDiscount(order);
    
    assertEquals(135.0, total, 0.01); // 10% de remise
}
```

2. **Code minimal pour passer le test :**

```java
public class OrderCalculator {
    public double calculateTotalWithDiscount(Order order) {
        double total = order.getItems().stream()
                           .mapToDouble(Item::getPrice)
                           .sum();
        if (total > 100) {
            return total * 0.9;
        }
        return total;
    }
}
```

3. **Refactorisation :**

```java
public class OrderCalculator {
    private static final double DISCOUNT_THRESHOLD = 100.0;
    private static final double DISCOUNT_RATE = 0.9;
    
    public double calculateTotalWithDiscount(Order order) {
        double total = calculateTotal(order);
        return applyDiscountIfEligible(total);
    }
    
    private double calculateTotal(Order order) {
        return order.getItems().stream()
                    .mapToDouble(Item::getPrice)
                    .sum();
    }
    
    private double applyDiscountIfEligible(double total) {
        if (total > DISCOUNT_THRESHOLD) {
            return total * DISCOUNT_RATE;
        }
        return total;
    }
}
```

---

## 14. Simplicité et Minimalisme

Dans *Clean Architecture*, Robert C. Martin insiste sur la **simplicité** comme un objectif fondamental.

### Règles

- **Faites le plus simple qui fonctionne** : Évitez les abstractions prématurées.
- **Appliquez YAGNI** (*You Aren’t Gonna Need It*) : N’ajoutez pas de code pour des besoins futurs hypothétiques.
- **Préférez les solutions directes** : Évitez les designs complexes sauf si nécessaire.
- **Réévaluez régulièrement** : Supprimez les fonctionnalités ou le code obsolète.

### Exemple

**Mauvais (sur-ingénierie) :**

```java
public abstract class AbstractOrderProcessor {
    protected abstract void validate(Order order);
    protected abstract void calculate(Order order);
    protected abstract void persist(Order order);
    
    public void process(Order order) {
        validate(order);
        calculate(order);
        persist(order);
    }
}

public class ConcreteOrderProcessor extends AbstractOrderProcessor {
    @Override
    protected void validate(Order order) {
        if (order.getItems().isEmpty()) {
            throw new IllegalArgumentException("Order cannot be empty");
        }
    }
    
    @Override
    protected void calculate(Order order) {
        // Rien pour l'instant
    }
    
    @Override
    protected void persist(Order order) {
        database.save(order);
    }
}
```

**Bon (simplicité) :**

```java
public class OrderProcessor {
    private final OrderRepository repository;
    
    public OrderProcessor(OrderRepository repository) {
        this.repository = repository;
    }
    
    public void process(Order order) {
        if (order.getItems().isEmpty()) {
            throw new IllegalArgumentException("Order cannot be empty");
        }
        repository.save(order);
    }
}
```

---

## 15. Modélisation Orientée Domaine

Inspiré par Eric Evans dans *Domain-Driven Design*, ce principe encourage à aligner le code avec le langage et les concepts du domaine métier.

### Règles

- **Utilisez un langage ubiquitaire** : Adoptez le vocabulaire du domaine dans le code.
- **Créez des entités riches** : Les objets métier doivent encapsuler la logique métier.
- **Isolez le domaine** : Séparez la logique métier des détails techniques.
- **Utilisez des agrégats** : Regroupez les entités liées pour maintenir la cohérence.

### Exemple

**Mauvais (modèle anémique) :**

```java
public class Order {
    private List<Item> items;
    private double total;
    
    // Getters et setters
    public List<Item> getItems() { return items; }
    public void setItems(List<Item> items) { this.items = items; }
    public double getTotal() { return total; }
    public void setTotal(double total) { this.total = total; }
}

public class OrderService {
    public void calculateTotal(Order order) {
        double total = order.getItems().stream()
                           .mapToDouble(Item::getPrice)
                           .sum();
        order.setTotal(total);
    }
}
```

**Bon (modèle riche) :**

```java
public class Order {
    private final List<Item> items;
    
    public Order() {
        this.items = new ArrayList<>();
    }
    
    public void addItem(Item item) {
        items.add(item);
    }
    
    public double calculateTotal() {
        return items.stream()
                    .mapToDouble(Item::getPrice)
                    .sum();
    }
    
    public List<Item> getItems() {
        return Collections.unmodifiableList(items);
    }
}

public class OrderService {
    private final OrderRepository repository;
    
    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }
    
    public void processOrder(Order order) {
        double total = order.calculateTotal();
        if (total <= 0) {
            throw new IllegalStateException("Order total must be positive");
        }
        repository.save(order);
    }
}
```

---

## 16. Utilisation Judicieuse des Patterns de Conception

Grady Booch et le *Gang of Four* (*Design Patterns*) soulignent l’importance d’utiliser les patterns de conception de manière appropriée.

### Règles

- **Appliquez les patterns avec parcimonie** : Utilisez un pattern uniquement si le problème le justifie.
- **Préférez les patterns simples** : Par exemple, Strategy ou Factory.
- **Documentez l’utilisation des patterns** : Expliquez pourquoi un pattern est utilisé.
- **Refactorisez si nécessaire** : Simplifiez si un pattern devient un obstacle.

### Exemple

**Mauvais (surutilisation de Factory) :**

```java
public interface OrderFactory {
    Order createOrder();
}

public class SimpleOrderFactory implements OrderFactory {
    @Override
    public Order createOrder() {
        return new Order();
    }
}

public class OrderService {
    private final OrderFactory factory;
    
    public OrderService(OrderFactory factory) {
        this.factory = factory;
    }
    
    public void createOrder() {
        Order order = factory.createOrder();
        // Logique
    }
}
```

**Bon (utilisation appropriée de Strategy) :**

```java
public interface DiscountStrategy {
    double applyDiscount(double total);
}

public class NoDiscountStrategy implements DiscountStrategy {
    @Override
    public double applyDiscount(double total) {
        return total;
    }
}

public class ThresholdDiscountStrategy implements DiscountStrategy {
    private static final double THRESHOLD = 100.0;
    private static final double RATE = 0.9;
    
    @Override
    public double applyDiscount(double total) {
        return total > THRESHOLD ? total * RATE : total;
    }
}

public class OrderCalculator {
    private final DiscountStrategy discountStrategy;
    
    public OrderCalculator(DiscountStrategy discountStrategy) {
        this.discountStrategy = discountStrategy;
    }
    
    public double calculateTotalWithDiscount(Order order) {
        double total = order.getItems().stream()
                           .mapToDouble(Item::getPrice)
                           .sum();
        return discountStrategy.applyDiscount(total);
    }
}
```

---

## 17. Documentation Vivante

Martin Fowler promeut l’idée d’une **documentation vivante**, intégrée au code et maintenue à jour.

### Règles

- **Intégrez la documentation dans le code** : Utilisez Javadoc et des README clairs.
- **Documentez l’intention et les décisions** : Expliquez les choix architecturaux ou techniques.
- **Maintenez la documentation à jour** : Mettez à jour lors des changements de code.
- **Préférez les exemples exécutables** : Les tests peuvent servir de documentation.

### Exemple

**Mauvais (documentation séparée et obsolète) :**

Un fichier `docs/order_processing.md` décrivant un workflow qui ne correspond plus au code.

**Bon (documentation vivante) :**

```java
/**
 * Service pour la gestion des commandes.
 * <p>
 * Ce service valide et sauvegarde les commandes en respectant les règles métier :
 * - Une commande ne peut pas être vide.
 * - Le total doit être positif.
 * </p>
 * @see Order
 * @see OrderRepository
 */
public class OrderService {
    private final OrderRepository repository;
    
    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }
    
    /**
     * Traite une commande en la validant et en la sauvegardant.
     * @param order la commande à traiter
     * @throws IllegalArgumentException si la commande est vide
     * @throws IllegalStateException si le total est négatif ou nul
     */
    public void processOrder(Order order) {
        validateOrder(order);
        repository.save(order);
    }
    
    private void validateOrder(Order order) {
        if (order.getItems().isEmpty()) {
            throw new IllegalArgumentException("Order cannot be empty");
        }
        if (order.calculateTotal() <= 0) {
            throw new IllegalStateException("Order total must be positive");
        }
    }
}
```

**Test servant de documentation :**

```java
@Test
void shouldThrowException_WhenOrderIsEmpty() {
    Order order = new Order();
    OrderService service = new OrderService(new InMemoryOrderRepository());
    
    assertThrows(IllegalArgumentException.class, () -> service.processOrder(order));
}
```

---

## 18. Principe de Cohérence d’Équipe

Kent Beck, dans *Extreme Programming Explained*, insiste sur l’importance de la cohérence dans les pratiques d’une équipe.

### Règles

- **Adoptez des conventions communes** : Par exemple, un style de nommage ou une structure de projet.
- **Standardisez les outils** : Utilisez les mêmes linters, formatteurs, et environnements de test.
- **Partagez les connaissances** : Documentez les décisions d’équipe et formez les nouveaux membres.
- **Revoyez régulièrement les pratiques** : Adaptez les conventions en fonction des retours.

### Exemple

**Mauvais (incohérence dans le nommage) :**

```java
public class OrderService {
    public void processOrder(Order ord) { /* ... */ } // "ord" au lieu de "order"
}

public class CustomerService {
    public void updateClient(Customer customer) { /* ... */ } // "client" au lieu de "customer"
}
```

**Bon (cohérence) :**

```java
public class OrderService {
    public void processOrder(Order order) {
        // Logique cohérente avec les conventions
    }
}

public class CustomerService {
    public void updateCustomer(Customer customer) {
        // Logique cohérente avec les conventions
    }
}
```

**Exemple de configuration d’équipe :**

Un fichier `.editorconfig` pour standardiser le formatage :

```ini
root = true

[*]
indent_style = space
indent_size = 4
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.java]
max_line_length = 120
```

---

## 19. Standardisation et Outils pour un Code Cohérent

Pour garantir un code cohérent, l’équipe doit adopter des conventions de nommage claires, utiliser des outils de standardisation pour le formatage et les tests, et intégrer des outils de documentation. Cette section fournit des recommandations pratiques et des exemples concrets pour Java.

### 19.1. Conventions de Nommage en Java

Adopter des conventions de nommage cohérentes améliore la lisibilité et réduit les erreurs d’interprétation. Les conventions doivent suivre les standards Java et être alignées sur le domaine métier.

#### Règles

- **Classes et Interfaces** : Utilisez des noms en `UpperCamelCase`, décrivant clairement le rôle (ex. : `OrderService`, `PaymentGateway`).
- **Méthodes** : Utilisez des verbes en `lowerCamelCase`, décrivant l’action (ex. : `calculateTotal`, `processOrder`).
- **Variables et Paramètres** : Utilisez des noms en `lowerCamelCase`, spécifiques et descriptifs (ex. : `customerId`, `orderItems`).
- **Constantes** : Utilisez `UPPER_SNAKE_CASE` pour les constantes statiques finales (ex. : `MAX_RETRIES`).
- **Packages** : Utilisez des noms en minuscules, suivant une hiérarchie claire (ex. : `com.company.order`).
- **Évitez les préfixes/suffixes inutiles** : Par exemple, ne nommez pas une classe `OrderClass` ou une interface `IOrder`.
- **Utilisez le langage ubiquitaire** : Intégrez le vocabulaire du domaine (ex. : `Invoice` au lieu de `Bill` si c’est le terme métier).

#### Exemple

**Mauvais :**

```java
public class ord { // Nom vague et non conforme
    int ID;
    List<String> Itms;
    
    public void calc() { // Nom de méthode vague
        // Logique
    }
}
```

**Bon :**

```java
package com.company.order;

public class Order {
    private final int orderId;
    private final List<Item> items;
    
    public Order(int orderId) {
        this.orderId = orderId;
        this.items = new ArrayList<>();
    }
    
    public void calculateTotal() {
        return items.stream()
                    .mapToDouble(Item::getPrice)
                    .sum();
    }
}
```

### 19.2. Outils de Standardisation pour le Formatage et les Tests

Les outils de standardisation garantissent un formatage uniforme et des tests robustes. Voici les recommandations pour Java.

#### Outils de Formatage

- **Checkstyle** : Vérifie la conformité aux conventions de codage.
- **Spotless** : Applique un formatage automatique (intégrable avec Maven/Gradle).
- **EditorConfig** : Standardise les paramètres d’éditeur (indentation, encodage).

**Exemple avec Checkstyle (configuration Maven)** :

Ajoutez Checkstyle à votre `pom.xml` :

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-checkstyle-plugin</artifactId>
    <version>3.3.1</version>
    <configuration>
        <configLocation>google_checks.xml</configLocation>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>check</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

**Fichier** `google_checks.xml` **(extrait)** :

```xml
<module name="Checker">
    <module name="FileTabCharacter"/>
    <module name="LineLength">
        <property name="max" value="120"/>
    </module>
    <module name="MethodName"/>
</module>
```

Exécutez `mvn checkstyle:check` pour valider le code. Cela garantit que les méthodes respectent les conventions de nommage et que les lignes ne dépassent pas 120 caractères.

**Exemple avec Spotless** :

Ajoutez Spotless à votre `pom.xml` :

```xml
<plugin>
    <groupId>com.diffplug.spotless</groupId>
    <artifactId>spotless-maven-plugin</artifactId>
    <version>2.43.0</version>
    <configuration>
        <java>
            <googleJavaFormat/>
            <removeUnusedImports/>
        </java>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>apply</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Exécutez `mvn spotless:apply` pour formater automatiquement le code selon le style Google Java Format.

#### Outils de Test

- **JUnit 5** : Framework standard pour les tests unitaires.
- **Mockito** : Pour créer des mocks et isoler les dépendances.
- **AssertJ** : Pour des assertions fluides et lisibles.

**Exemple de test avec JUnit 5, Mockito, et AssertJ** :

```java
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.*;

public class OrderServiceTest {
    private final OrderRepository repository = mock(OrderRepository.class);
    private final OrderService service = new OrderService(repository);
    
    @Test
    void shouldSaveOrder_WhenValid() {
        Order order = new Order();
        order.addItem(new Item(50.0));
        
        service.processOrder(order);
        
        verify(repository).save(order);
        assertThat(order.calculateTotal()).isEqualTo(50.0);
    }
    
    @Test
    void shouldThrowException_WhenOrderIsEmpty() {
        Order order = new Order();
        
        assertThatThrownBy(() -> service.processOrder(order))
            .isInstanceOf(IllegalArgumentException.class)
            .hasMessage("Order cannot be empty");
    }
}
```

Ajoutez les dépendances dans `pom.xml` :

```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.3</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <version>5.12.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>3.26.3</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 19.3. Outils de Documentation

Une documentation claire et maintenable est essentielle. Voici les outils recommandés pour Java.

- **Javadoc** : Génère une documentation à partir des commentaires dans le code.
- **Doxygen** : Pour une documentation plus avancée, incluant des diagrammes.
- **MkDocs** : Pour créer une documentation statique à partir de Markdown.

**Exemple avec Javadoc** :

```java
/**
 * Service pour la gestion des commandes.
 * <p>
 * Ce service permet de valider et sauvegarder des commandes en respectant les règles métier suivantes :
 * <ul>
 *     <li>La commande ne peut pas être vide.</li>
 *     <li>Le total doit être positif.</li>
 * </ul>
 * </p>
 * @author Équipe de développement
 * @since 1.0
 * @see Order
 * @see OrderRepository
 */
public class OrderService {
    private final OrderRepository repository;
    
    /**
     * Construit une instance de {@code OrderService}.
     * @param repository le référentiel pour sauvegarder les commandes
     */
    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }
    
    /**
     * Traite une commande en la validant et en la sauvegardant.
     * @param order la commande à traiter
     * @throws IllegalArgumentException si la commande est vide
     * @throws IllegalStateException si le total est négatif ou nul
     */
    public void processOrder(Order order) {
        validateOrder(order);
        repository.save(order);
    }
    
    private void validateOrder(Order order) {
        if (order.getItems().isEmpty()) {
            throw new IllegalArgumentException("Order cannot be empty");
        }
        if (order.calculateTotal() <= 0) {
            throw new IllegalStateException("Order total must be positive");
        }
    }
}
```

Générez la documentation avec Maven :

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>3.6.3</version>
    <executions>
        <execution>
            <id>generate-javadoc</id>
            <goals>
                <goal>javadoc</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Exécutez `mvn javadoc:javadoc` pour générer la documentation HTML dans `target/site/apidocs`.

**Exemple avec MkDocs** :

Créez un fichier `docs/index.md` pour compléter la Javadoc :

```markdown
# Documentation du Projet

## Aperçu
Ce projet implémente un système de gestion des commandes basé sur les principes de *Clean Code*.

## Architecture
Le système suit une architecture en couches :
- **Domaine** : Contient les entités comme `Order`.
- **Application** : Contient les services comme `OrderService`.
- **Infrastructure** : Contient les implémentations comme `OrderRepository`.

## Exemple d’Utilisation
```java
Order order = new Order();
order.addItem(new Item(50.0));
OrderService service = new OrderService(new InMemoryOrderRepository());
service.processOrder(order);
```

```

Configurez `mkdocs.yml` :

```yaml
site_name: Documentation du Projet
theme: material
docs_dir: docs
site_dir: site
```

Exécutez `mkdocs serve` pour prévisualiser la documentation ou `mkdocs build` pour générer un site statique.

---

## 20. Conclusion

Cette charte, enrichie par les principes de Robert C. Martin (*Clean Code*, *The Clean Coder*, *Clean Architecture*), les contributions d’Eric Evans, Martin Fowler, Kent Beck, Grady Booch, et des outils modernes de standardisation, vise à instaurer une **culture de code propre**, un **professionnalisme**, et une **collaboration efficace** dans l’équipe. Chaque développeur doit :

- **Relire son code** avant soumission, comme s’il était lu par un collègue.
- **Participer aux revues de code** pour partager les bonnes pratiques.
- **Apprendre continuellement** en s’appuyant sur les travaux des experts et en appliquant ces principes.
- **Adopter TDD, la simplicité, et la cohérence** avec des outils comme Checkstyle, JUnit, et Javadoc.

En suivant ces règles, nous construirons un code **robuste**, **lisible** et **évolutif**, facilitant la maintenance et l’innovation.

---

*Inspiré par Clean Code, The Clean Coder, Clean Architecture de Robert C. Martin, Domain-Driven Design d’Eric Evans, les écrits de Martin Fowler, Extreme Programming Explained de Kent Beck, et Design Patterns du Gang of Four. Dernière mise à jour : 19 avril 2025.*
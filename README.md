# TP02 — IoC et injection de dépendances (Spring Core)

**Projet :** springcoredemo  
**Module :** IRT — Spring Boot 4  
**Java :** 25

## 1. Différence entre IoC et DI

L'IoC (Inversion of Control) est le principe selon lequel la création et la gestion des objets sont confiées à un conteneur externe plutôt qu'au code de l'application.

La DI (Dependency Injection) est un mécanisme qui permet au conteneur d'injecter les dépendances nécessaires dans un objet.

Avec Spring, le contrôleur ne crée pas directement `CricketCoach` avec `new CricketCoach()`. Le conteneur `ApplicationContext` crée les beans et fournit la dépendance demandée dans le constructeur.

## 2. Pourquoi préférer l'injection par constructeur ?

L'injection par constructeur présente plusieurs avantages :

- **Immuabilité :** les dépendances peuvent être déclarées `final`.
- **Testabilité :** on peut fournir facilement des dépendances lors des tests.
- **Validité de l'objet :** les dépendances obligatoires sont fournies dès la construction.
- **Lisibilité :** les dépendances nécessaires sont visibles dans le constructeur.

L'injection par champ est plus difficile à tester et masque les dépendances de la classe.

## 3. NoUniqueBeanDefinitionException et solutions

Cette exception survient lorsque Spring doit injecter un bean d'un type donné, mais trouve plusieurs candidats sans pouvoir en sélectionner un seul.

Deux solutions sont possibles :

1. `@Qualifier("cricketCoach")` désigne explicitement le bean à injecter.
2. `@Primary` désigne le bean choisi par défaut lorsqu'il existe plusieurs candidats.

Si les deux annotations sont présentes et que le bean indiqué par `@Qualifier` est un candidat valide, `@Qualifier` est prioritaire sur `@Primary`.

## 4. Identifiant par défaut d'un bean

Par défaut, Spring utilise le nom de la classe avec sa première lettre en minuscule. Par exemple, `CricketCoach` devient `cricketCoach`.

On peut définir un identifiant personnalisé avec `@Component("monCoach")` ou `@Bean("aquatic")`.

## 5. Avantages et inconvénients de @Lazy

L'initialisation paresseuse diffère la création d'un bean jusqu'à ce qu'il soit nécessaire. Elle peut réduire le travail réalisé au démarrage et éviter de créer immédiatement des beans rarement utilisés.

Son principal inconvénient est que certaines erreurs de configuration ne sont détectées qu'au moment où le bean est demandé. Il faut donc l'utiliser avec discernement.

La propriété `spring.main.lazy-initialization=true` active globalement l'initialisation paresseuse.

## 6. Différence entre singleton et prototype

Avec le scope `singleton`, les deux injections de `CricketCoach` renvoient normalement la même instance : `/check` retourne `true`.

Avec le scope `prototype`, Spring fournit une nouvelle instance à chaque demande au conteneur. Les deux injections réalisées lors de la création du contrôleur donnent alors deux références différentes : `/check` retourne `false`.

Cela démontre que le scope détermine la manière dont Spring gère les instances des beans.

## 7. Cycle de vie d'un bean singleton

L'ordre général est :

1. Construction de l'objet.
2. Injection des dépendances.
3. Exécution de la méthode annotée `@PostConstruct`.
4. Utilisation du bean.
5. Exécution de la méthode annotée `@PreDestroy` lors de la destruction du bean et de l'arrêt normal du contexte Spring.

Spring ne gère pas la destruction des beans prototype après les avoir remis au client. Leur méthode `@PreDestroy` n'est donc pas appelée automatiquement par le conteneur.

## 8. Quand préférer @Bean à @Component ?

On préfère souvent `@Bean` dans une classe `@Configuration` dans les situations suivantes :

1. Pour enregistrer une classe tierce qu'on ne peut pas annoter avec `@Component`.
2. Pour effectuer une création complexe ou utiliser une méthode factory.
3. Pour créer plusieurs instances du même type avec des configurations différentes.

## Tableau des scopes

| Scope | Description |
|---|---|
| `singleton` | Une instance par conteneur Spring et par définition de bean. |
| `prototype` | Une nouvelle instance à chaque demande au conteneur. |
| `request` | Une instance par requête HTTP. |
| `session` | Une instance par session HTTP. |
| `application` | Une instance par `ServletContext`. |

Les scopes `request`, `session` et `application` sont destinés aux applications web.

## Conclusion

Ce TP m'a permis de comprendre le conteneur IoC de Spring, l'injection de dépendances par constructeur, la résolution de l'ambiguïté avec `@Qualifier` et `@Primary`, l'initialisation paresseuse, les scopes des beans, leur cycle de vie et la déclaration de beans avec `@Configuration` et `@Bean`.

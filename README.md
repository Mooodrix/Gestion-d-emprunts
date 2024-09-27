# Application de Gestion de Réservation de Matériel

## Sommaire
1. [Contexte](#contexte)
2. [Phase d’idéation](#phase-did%C3%A9ation)
3. [Modèle de données](#mod%C3%A8le-de-donn%C3%A9es)
4. [Règles de gestion](#r%C3%A8gles-de-gestion)
5. [Dictionnaire de données](#dictionnaire-de-donn%C3%A9es)
6. [Modèle Conceptuel de Données (MCD)](#mod%C3%A8le-conceptuel-de-donn%C3%A9es-mcd)
7. [Modèle Physique de Données (MPD)](#mod%C3%A8le-physique-de-donn%C3%A9es-mpd)
8. [Choix des outils et de la stack technique](#choix-des-outils-et-de-la-stack-technique)
9. [Versioning](#versioning)
10. [Connexion à la BDD](#connexion-%C3%A0-la-bdd)
11. [Mise en place du schéma](#mise-en-place-du-sch%C3%A9ma)
12. [Peuplement de la base de données](#peuplement-de-la-base-de-donn%C3%A9es)
13. [Préparation des vues](#pr%C3%A9paration-des-vues)
14. [Authentification avec Symfony](#authentification-avec-symfony)
15. [Interface d’administration](#interface-dadministration)
16. [How to run the application](#how-to-run-the-application)
17. [Remerciements](#remerciements)

---

### Contexte

Le projet consiste à créer une application de gestion de réservation de matériel pour le laboratoire `myDiL`. Le cas d’usage principal permet à un utilisateur d'emprunter du matériel au sein de l'école. L'équipe était constituée de quatre développeurs utilisant des technologies partagées.

### Phase d’idéation

Durant la première journée, nous avons listé plusieurs cas d'utilisation pour identifier les fonctionnalités prioritaires. Certaines idées ont été écartées pour respecter les délais. L'application finale se veut simple et fonctionnelle, avec la possibilité d’ajouter des fonctionnalités supplémentaires si le temps le permet.

### Modèle de données

Nous avons opté pour une approche `data first` afin d’identifier les informations clés avant de passer au développement. La méthode **MERISE** a été utilisée pour définir et organiser notre modèle de données, avec une vue claire des entités, de leurs relations, et des flux de données.

Les entités identifiées incluent les utilisateurs, les équipements, les prêts, et les stocks. La conception a permis d’établir les interdépendances et de répondre aux besoins métiers.

### Règles de gestion

Les règles suivantes ont été définies :

1. **Utilisateur**
   - Chaque utilisateur est identifié par un `user_id` unique.
   - Attributs : `email`, `rôle` (administrateur ou utilisateur), et `password`.
   - Un utilisateur peut avoir `0` ou plusieurs prêts en cours.

2. **Équipement**
   - Chaque équipement est identifié par un `equipement_id` unique.
   - Attributs : `nom`, `description`, `type`, `disponibilité`, et `référence`.
   - Un équipement peut être lié à `0` ou `1` prêt à la fois.

3. **Prêt**
   - Identifié par un `loan_id` unique.
   - Attributs : `user_id` de l’utilisateur, `equipement_id` des équipements, `date de début`, `date de fin prévue`, et `statut` (en cours, terminé, annulé, en retard).

4. **Stock**
   - Représente le nombre d'unités disponibles.
   - Doit être mis à jour lors de chaque emprunt et retour d’équipement.
   - Un seuil d’alerte est fixé pour réapprovisionnement.
   - Si le stock d'un équipement est `0`, aucun nouvel emprunt n'est autorisé.

### Dictionnaire de données

Le dictionnaire de données documente les attributs de chaque entité et leur format. Ce document est essentiel pour garantir la cohérence des données lors de l’implémentation.

### Modèle Conceptuel de Données (MCD)

Le MCD définit les entités et leurs relations de manière abstraite. Les cardinalités définissent les contraintes entre les différentes entités.

### Modèle Physique de Données (MPD)

Le MPD est la traduction du MCD en tables relationnelles. Chaque table correspond à une entité, et les relations sont matérialisées par des clés étrangères.

### Choix des outils et de la stack technique

- **Outil de modélisation** : [Excalidraw](https://excalidraw.com), un outil collaboratif pour schématiser le modèle de données.
- **Framework** : [Symfony](https://symfony.com) pour sa flexibilité et sa rapidité de développement.
- **ORM** : [Doctrine](https://www.doctrine-project.org) pour la gestion des entités et la génération des requêtes SQL.
- **Système de versioning** : GitHub, pour la gestion des branches et le suivi des modifications.

### Versioning

Nous avons utilisé **Git** avec un modèle de branches. Chaque développeur travaillait sur sa propre branche dérivée de `main`. Une fois une fonctionnalité terminée, une **pull request** était créée et revue par les autres membres avant d’être fusionnée.

### Connexion à la BDD

Nous avons configuré Symfony pour se connecter à une base de données MySQL en utilisant le `Maker Bundle` pour générer les entités et les configurations de base de données.

### Mise en place du schéma

Nous avons utilisé Doctrine pour créer les entités et générer les tables correspondantes. Chaque entité est mappée à une table via des annotations, et les fichiers de migrations contiennent les requêtes SQL nécessaires.

### Peuplement de la base de données

Les données de test ont été créées à l’aide de fixtures. Ces fixtures permettent de peupler la base de données avec des objets de test respectant les contraintes de validation.

### Préparation des vues

Les vues sont générées via des **Controllers** qui suivent le modèle **MVC** de Symfony. Les contrôleurs définissent les routes, collectent les données nécessaires, et les passent à des templates Twig pour le rendu.

### Authentification avec Symfony

Symfony propose un composant `Security` pour gérer l'authentification et les autorisations. Nous avons utilisé les commandes `make:user` et `make:auth` pour générer les classes nécessaires à la gestion des utilisateurs.

### Interface d’administration

L’interface d’administration a été mise en place avec le bundle **EasyAdmin** de Symfony. Ce bundle permet de gérer facilement les entités avec un CRUD complet, configurable via les annotations et les fichiers de configuration.

### How to run the application

1. **Installer les dépendances** :
    ```bash
    composer install
    ```
2. **Configurer la base de données** :
   - Créer un fichier `.env.local` à la racine du projet avec les informations de connexion.

3. **Lancer les migrations** :
    ```bash
    php bin/console doctrine:migrations:migrate
    ```
4. **Peupler la base de données** :
    ```bash
    php bin/console doctrine:fixtures:load
    ```
5. **Lancer le serveur** :
    ```bash
    symfony serve
    ```

Accéder à l’application via l’URL fournie par la CLI.

### Remerciements

Nous remercions tous les participants et les intervenants du workshop pour leur soutien et leur engagement. Ce projet a été une belle opportunité pour collaborer et approfondir nos connaissances techniques tout en respectant un calendrier serré.

**Équipe** : TEAMS LEBANIE

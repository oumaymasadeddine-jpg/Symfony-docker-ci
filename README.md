# Symfony Product API - Test Technique

API REST avec back-office pour la gestion de produits et catégories.

## Stack Technique

- Symfony 6.4
- API Platform
- EasyAdmin
- Messenger (traitement asynchrone)
- Doctrine ORM
- MySQL 8.0
- Docker

## Fonctionnalités

- API REST complète (GET, POST, PATCH)
- Relation ManyToMany Product - Category
- Back-office d'administration
- Logs asynchrones des modifications produits
- Filtres API Platform sur les produits
- Fixtures avec Foundry (10 produits + 5 catégories)

## Installation avec Docker
```bash
# Cloner le projet
git clone https://github.com/oumaymasadeddine-jpg/Dhygietal-test.git
cd Dhygietal-test

# Lancer les containers
docker-compose up -d

# Attendre le démarrage des services (environ 30 secondes)
```

L'application sera accessible sur :
- API : http://localhost:8000/api
- Back-office : http://localhost:8000/admin

## Installation en local
```bash
# Installer les dépendances
composer install

# Configurer la base de données dans .env
DATABASE_URL="mysql://root:@127.0.0.1:3306/symfony_product_api"

# Créer la base de données
php bin/console doctrine:database:create
php bin/console doctrine:migrations:migrate

# Configurer Messenger
php bin/console messenger:setup-transports

# Charger les fixtures (optionnel)
php bin/console doctrine:fixtures:load

# Lancer le serveur
symfony server:start

# Dans un autre terminal : lancer le worker Messenger
php bin/console messenger:consume async -vv
```

## Tester l'API

### Interface Swagger

Accédez à http://localhost:8000/api pour une interface interactive complète.

### Exemples curl

**Créer une catégorie** :
```bash
curl -X POST http://localhost:8000/api/categories \
  -H "Content-Type: application/json" \
  -d '{"designation": "Electronics"}'
```

**Créer un produit avec catégories** :
```bash
curl -X POST http://localhost:8000/api/products \
  -H "Content-Type: application/json" \
  -d '{"designation": "Laptop Dell XPS", "categories": ["/api/categories/1"]}'
```

**Lister les produits** :
```bash
curl http://localhost:8000/api/products
```

**Modifier un produit** :
```bash
curl -X PATCH http://localhost:8000/api/products/1 \
  -H "Content-Type: application/merge-patch+json" \
  -d '{"designation": "Laptop Updated", "categories": ["/api/categories/1", "/api/categories/2"]}'
```

**Filtrer par designation** :
```bash
curl "http://localhost:8000/api/products?designation=Laptop"
```

**Filtrer par catégorie** :
```bash
curl "http://localhost:8000/api/products?categories.id=1"
```

## Vérifier les logs asynchrones

Les modifications de produits génèrent automatiquement des logs en base de données via Messenger.
```bash
# Via Docker
docker-compose exec php php bin/console doctrine:query:sql "SELECT * FROM product_log"

# En local
php bin/console doctrine:query:sql "SELECT * FROM product_log"
```

## Endpoints API

| GET | /api/products | Liste tous les produits |
| GET | /api/products/{id} | Détails d'un produit |
| POST | /api/products | Créer un produit |
| PATCH | /api/products/{id} | Modifier un produit |
| GET | /api/categories | Liste toutes les catégories |
| POST | /api/categories | Créer une catégorie |

## Back-office EasyAdmin

Accédez au back-office sur http://localhost:8000/admin

Fonctionnalités disponibles :
- CRUD complet Product avec sélection multiple de catégories
- CRUD complet Category
- Interface intuitive et responsive

## Architecture du projet
```
src/
├── Controller/Admin/     # Controllers EasyAdmin
├── DataFixtures/         # Fixtures Doctrine
├── Entity/               # Entités Doctrine
├── EventListener/        # Listeners pour Messenger
├── Factory/              # Foundry factories
├── Message/              # Messages Messenger
├── MessageHandler/       # Handlers Messenger
├── Repository/           # Repositories Doctrine
└── Story/                # Stories Foundry
```

## Commandes utiles
```bash
# Lancer les migrations
php bin/console doctrine:migrations:migrate

# Charger les fixtures
php bin/console doctrine:fixtures:load

# Vider le cache
php bin/console cache:clear

# Voir les routes
php bin/console debug:router

# Consommer les messages Messenger
php bin/console messenger:consume async -vv

# Créer la base de test
php bin/console doctrine:database:create --env=test
php bin/console doctrine:migrations:migrate --env=test
```

## Bonus implémentés

**Bonus A - Filtres API Platform**
- Filtre par designation (recherche partielle)
- Filtre par categories.id

**Bonus B - Fixtures avec Foundry**
- 5 catégories (Electronics, Books, Clothing, Food, Sports)
- 10 produits avec associations aléatoires
- Utilisation du pattern Story

## Technologies et bonnes pratiques

- Architecture hexagonale
- Separation of concerns avec EventListener
- Validation avec Assert
- Groupes de sérialisation API Platform
- Traitement asynchrone avec Messenger
- Fixtures avec design pattern Story

---

Développé par Oumayma Sadeddine dans le cadre d'un test technique Symfony.
=======
>>>>>>> Stashed changes

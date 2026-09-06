---
name: ci-cd
description: CI/CD pipelines and deployment workflows for this PHP/Laravel project. Trigger for "CI", "CD", "deploy", "pipeline", "GitHub Actions", "GitLab CI", "build", "test pipeline", "deployment", "automation".
---

# CI/CD

CI/CD pipelines and deployment workflows for the **PHP/Laravel** project.

## Stack

| Layer | Technology |
|-------|-----------|
| **Language** | PHP |
| **Framework** | Laravel |
| **Testing** | Pest |
| **Formatting** | pint |
| **Linting** | PHP CS Fixer |

## When to Use

- **Trigger phrases**: "CI", "CD", "deploy", "pipeline", "build", "test pipeline", "automation"
- **Tasks**: Setting up CI/CD, writing pipeline configs, debugging builds, deployment scripts

## Core Concepts

- **Pest** for testing — run with `./vendor/bin/pest`
- **pint** for formatting — run with `./vendor/bin/pint`
- **Composer** for dependency management
- **Artisan** for Laravel-specific commands

## Pipeline Stages

### 1. Lint & Format
```bash
./vendor/bin/pint --quiet
./vendor/bin/php-cs-fixer fix --dry-run --diff
```

### 2. Test
```bash
./vendor/bin/pest
./vendor/bin/pest --coverage
```

### 3. Build & Deploy
```bash
composer install --optimize-autoloader --no-dev
php artisan migrate --force
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

## Common CI/CD Commands

```bash
# Run tests
./vendor/bin/pest

# Run tests with coverage
./vendor/bin/pest --coverage

# Code formatting
./vendor/bin/pint

# Check formatting
./vendor/bin/pint --dry-run

# Run artisan commands
php artisan migrate
php artisan db:seed
php artisan optimize:clear

# Composer
composer install
composer update --no-dev
```

## CI/CD Patterns

### GitHub Actions
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
          coverage: xdebug
      - run: composer install
      - run: ./vendor/bin/pest
      - run: ./vendor/bin/pint --dry-run
```

### GitLab CI
```yaml
test:
  image: php:8.2
  script:
    - apt-get update && apt-get install -y unzip
    - curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
    - composer install
    - ./vendor/bin/pest
    - ./vendor/bin/pint --dry-run
```

## Quick Decisions

| Question | Answer |
|----------|--------|
| Which test framework? | Pest |
| Which formatter? | pint |
| PHP version? | 8.2 |
| Where do tests live? | `tests/` |
| Where are configs? | `config/` |
| Deployment command? | `composer install --optimize-autoloader` |

## Related Skills

- `db-migrator` - Database migrations and schema management
- `project-architect` - Project structure and conventions
- `tester` - Test patterns and practices

---

**Delete this section** when customizing for your stack.

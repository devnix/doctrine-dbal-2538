# [Doctrine DBAL Issue #4245](https://github.com/doctrine/dbal/issues/4245)

This is a small repo replicating a bug.

## Setting up

```docker-compose up -d```

```symfony console doctrine:database:create```

```symfony console doctrine:query:sql 'CREATE TABLE `configuration`( `key_name` varchar(60) COLLATE utf8mb4_unicode_ci NOT NULL, `value` longtext COLLATE utf8mb4_unicode_ci NOT NULL) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci; ALTER TABLE `configuration` ADD PRIMARY KEY (`key_name`), ADD UNIQUE KEY `key_value` (`key_name`);'```

## Replicating

Generate the entities: ```symfony console doctrine:mapping:import App\\Entity annotation --path=src/Entity```

Create a migration to check if everything is OK: ```symfony console doctrine:migrations:diff```

A migration like this is generated. Everything is not OK:

```php
<?php

declare(strict_types=1);

namespace DoctrineMigrations;

use Doctrine\DBAL\Schema\Schema;
use Doctrine\Migrations\AbstractMigration;

/**
 * Auto-generated Migration: Please modify to your needs!
 */
final class Version20200903070614 extends AbstractMigration
{
    public function getDescription() : string
    {
        return '';
    }

    public function up(Schema $schema) : void
    {
        // this up() migration is auto-generated, please modify it to your needs
        $this->addSql('ALTER TABLE configuration CHANGE key_name key_name VARCHAR(60) NOT NULL');
    }

    public function down(Schema $schema) : void
    {
        // this down() migration is auto-generated, please modify it to your needs
        $this->addSql('ALTER TABLE configuration CHANGE key_name key_name VARCHAR(60) CHARACTER SET utf8mb4 NOT NULL COLLATE `utf8mb4_unicode_ci`');
    }
}
```

So, let's run all the pending migrations ```symfony console doctrine:migrations:migrate```

And, there sould be no diffs now. Right!? ```symfony console doctrine:migrations:diff```

Nope, a new identical migration is generated. The only change is the timestamp on the class name:

```php
<?php

declare(strict_types=1);

namespace DoctrineMigrations;

use Doctrine\DBAL\Schema\Schema;
use Doctrine\Migrations\AbstractMigration;

/**
 * Auto-generated Migration: Please modify to your needs!
 */
final class Version20200903070834 extends AbstractMigration
{
    public function getDescription() : string
    {
        return '';
    }

    public function up(Schema $schema) : void
    {
        // this up() migration is auto-generated, please modify it to your needs
        $this->addSql('ALTER TABLE configuration CHANGE key_name key_name VARCHAR(60) NOT NULL');
    }

    public function down(Schema $schema) : void
    {
        // this down() migration is auto-generated, please modify it to your needs
        $this->addSql('ALTER TABLE configuration CHANGE key_name key_name VARCHAR(60) CHARACTER SET utf8mb4 NOT NULL COLLATE `utf8mb4_unicode_ci`');
    }
}
```
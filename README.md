# Generate Recommendation List For Eloquent models

[![WOSPM Checker](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/wospm.yml/badge.svg)](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/wospm.yml) [![Codestyle Check](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/phpcs.yml/badge.svg)](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/phpcs.yml) [![Tests](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/tests.yml/badge.svg)](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/tests.yml) ![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/umutphp/laravel-model-recommendation) [![Markdown Linter](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/mardown-lint.yml/badge.svg)](https://github.com/umutphp/laravel-model-recommendation/actions/workflows/mardown-lint.yml)

This package generates recommendation list for elequent models. It provides a simple API to work with to generate and list recommendations for a model. The package uses co-occurrence of models in data table under same group to make a recommendation.

![Laravel Model Recommendation](./assets/images/logo.png)

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [How To Install](#how-to-install)
  - [Requiring The Library](#requiring-the-library)
  - [Prepare The Database](#prepare-the-database)
  - [Add The Service Provider](#add-the-service-provider)
  - [Add The Trait And Interface To The Model](#add-the-trait-and-interface-to-the-model)
- [How To Use](#how-to-use)
  - [Glosary](#glosary)
  - [Use Case 1](#use-case-1)
  - [Use Case 2](#use-case-2)
- [Contributing](#contributing)
- [Security Vulnerabilities](#security-vulnerabilities)
- [Credits](#credits)
- [License](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## How To Install

### Requiring The Library

```bash
composer require "umutphp/laravel-model-recommendation"
```

### Prepare The Database

```bash
php artisan vendor:publish --provider="Umutphp\LaravelModelRecommendation\ModelRecommendationServiceProvider"
php artisan migrate
```

### Add The Service Provider

Append the following line to the `providers` array in `config/app.php`;

```php
Umutphp\LaravelModelRecommendation\ModelRecommendationServiceProvider::class,
```

### Add The Trait And Interface To The Model

Add `HasRecommendation` trait and `InteractWithRecommendation` interface to the class definition. Please do not forget to implement the functions of the interface.

Here are the functions to be implemented:

* `getRecommendationDataTable()`: Returns the name of the data table.
* `getRecommendationDataField()`: Returns the name of the data field.
* `getRecommendationGroupField()`: Returns the name of the group field.
* `getRecommendationCount()`: Returns the number of the items in the recommendation list.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Umutphp\LaravelModelRecommendation\InteractWithRecommendation;
use Umutphp\LaravelModelRecommendation\HasRecommendation;

class ModelName extends Model implements InteractWithRecommendation
{
    use HasFactory, HasRecommendation;

    public static function getRecommendationDataTable() :string
    {
        return 'data_table';
    }
    public static function getRecommendationDataField() :string
    {
        return 'data_field';
    }
    public static function getRecommendationGroupField() :string
    {
        return 'group_field';
    }
    public static function getRecommendationCount() :int
    {
        return 5;
    }
}
```

## How To Use

Here are a few short examples of what you can do.

* To generate recommendation list for the given model type.

```php
ModelName::generateRecommendations();
```

* To get the list of recommended models for a model. This function can be called in an Artisan command and scheduled to run periodically.

```php
$recommendations = $model->getRecommendations();
```

For these functions (generateRecommendations() and getRecommendations()) to be executed correctly, you should implement the four functions described in [Add The Trait And Interface To The Model](#add-the-trait-and-interface-to-the-model) section. Following use cases may help you understand the functions.

### Glosary

* **Data Table**: The table that stores the occurance (mosprobably with the ID of the model) of the models. Please look at the use cases for the example tables.
* **Group Field**: The field of the table that defines the co-occurance of the models.
* **Data Field**: The field that identifies the models.

### Use Case 1

You want to get recommendations for products (sold together) in an e-commerce site. You have `Product` model and `order_products` table storing the relation between orders and products.

**order_products** table;

| Field1 | Field2 | Field3 | Field4 | Field5 | Field6 |
| --- | --- | --- | --- | --- | --- |
| id | order_id | product_id | product_count | created_at | updated_at |

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Umutphp\LaravelModelRecommendation\InteractWithRecommendation;
use Umutphp\LaravelModelRecommendation\HasRecommendation;

class Product extends Model implements InteractWithRecommendation
{
    use HasFactory, HasRecommendation;

    public static function getRecommendationDataTable() :string
    {
        return 'order_products';
    }
    public static function getRecommendationDataField() :string
    {
        return 'product_id';
    }
    public static function getRecommendationGroupField() :string
    {
        return 'order_id';
    }
    public static function getRecommendationCount() :int
    {
        return 5;
    }
}
```

### Use Case 2

You want to get recommendations for users in a dating site. You have `User` model and `user_friends` table storing the relation between users.

**user_friends** table;

| Field1 | Field2 | Field3 | Field4 | Field5 |
| --- | --- | --- | --- | --- |
| id | user_id | friend_id | created_at | updated_at |

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Umutphp\LaravelModelRecommendation\InteractWithRecommendation;
use Umutphp\LaravelModelRecommendation\HasRecommendation;

class User extends Model implements InteractWithRecommendation
{
    use HasFactory, HasRecommendation;

    public static function getRecommendationDataTable() :string
    {
        return 'user_friends';
    }
    public static function getRecommendationDataField() :string
    {
        return 'friend_id';
    }
    public static function getRecommendationGroupField() :string
    {
        return 'user_id';
    }
    public static function getRecommendationCount() :int
    {
        return 5;
    }
}
```

## Contributing

Please see [CONTRIBUTING](.github/CONTRIBUTING.md) for details.

## Security Vulnerabilities

Please review [our security policy](../../security/policy) on how to report security vulnerabilities.

## Credits

- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

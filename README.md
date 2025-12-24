# Slotsgateway PHP Client

**Slotsgateway PHP Client** is a PHP library designed to simplify integration with the **Slotsgateway API**.

It supports **PHP 5.0+** and uses **GuzzleHTTP** for HTTP communication.  
The client can be used in any PHP-based project or framework, including **Laravel**, **Lumen**, **Yii**, and similar frameworks.

---

## Requirements

- PHP **5.0 or higher**
- Composer
- GuzzleHTTP

---

## Installation

Install the package via Composer:

```bash
composer require slotsgateway/slotsgateway-php-client
```

---

## API Client Overview

The `ApiClient` class provides direct wrappers for all Slotsgateway API methods.

### Available API Methods

```php
// Open a real-money game session
getGame(
  $username,
  $userpassword,
  $game_id,
  $currency,
  $homeurl,
  $cashierurl,
  $play_for_fun,
  $lang
);

// Open a demo (fun-play) game session
getGameDemo(
  $game_id,
  $currency,
  $homeurl,
  $cashierurl,
  $lang
);

// Retrieve the game list
getGameList(
  $currency,
  $list_type
);

// Create or initialize a player
createPlayer(
  $username,
  $userpassword,
  $usernickname,
  $currency
);

// Add free spins to a player
addFreeRounds(
  $username,
  $userpassword,
  $game_id,
  $currency,
  $freespins,
  $betlevel
);

// Retrieve free spins history
getFreeRounds(
  $username,
  $userpassword,
  $currency
);

// Delete free spins for a specific game
deleteFreeRounds(
  $gameid,
  $username,
  $userpassword,
  $currency
);

// Delete all free spins for a player
deleteAllFreeRounds(
  $username,
  $userpassword,
  $currency
);
```

---

## Helper Functions

The package includes a `Helpers` class to simplify callback handling and common data transformations.

### Callback Helpers

```php
// Validate callback signature using key, timestamp, and your API salt key
isValidKey($key, $timestamp, $salt);

// Return a valid balance response (integer, cents)
balanceResponse($intBalance);

// Return an insufficient balance response
insufficientBalance($intBalance);

// Return a generic processing error response
processingError();
```

### General Utility Helpers

```php
// Check if an API response contains an error
responseHasError($apiResponse);

// Convert a JSON object into an associative array
morphJsonToArray($input);

// Convert float balance (e.g. 2.00) to integer cents (200)
floatToIntHelper($floatValue);

// Convert integer cents back to float (2 decimals)
intToFloatHelper($intValue);
```

---

## Usage Examples

### Basic PHP Example

```php
<?php
use slotsgateway\client;

require __DIR__ . '/vendor/autoload.php';

$client = new client\ApiClient([
  "endpoint"     => "https://secretendpoint.com",
  "api_login"    => "12345",
  "api_password" => "12345",
]);

var_export($client->getGameList("USD", 1));
```

---

## Laravel Example

### Routes

```php
// routes/web.php
Route::get('/slotsgateway_example/gamelist', [\App\Http\Controllers\SlotsgatewayController::class, 'gamelist']);
Route::get('/slotsgateway_example/gameflow', [\App\Http\Controllers\SlotsgatewayController::class, 'gameflow']);
```

### Controller

```php
<?php

namespace App\Http\Controllers;

use slotsgateway\client;

class SlotsgatewayController extends Controller
{
  protected $client;
  protected $helpers;

  public function __construct()
  {
    $this->client = new client\ApiClient([
      "endpoint"     => "https://secretendpoint.net",
      "api_login"    => "12345",
      "api_password" => "12345",
    ]);

    $this->helpers = new client\Helpers();
  }

  public function gamelist()
  {
    return $this->client->getGameList("USD", 1);
  }

  public function gameflow()
  {
    $createPlayer = $this->client->createPlayer(
      "playerId1337",
      "playerPassword",
      "Tiernan",
      "USD"
    );

    if ($this->helpers->responseHasError($createPlayer)) {
      return $createPlayer;
    }

    return $this->client->getGame(
      "playerId1337",
      "playerPassword",
      "platipus/egyptiangold",
      "USD",
      "https://casino.com",
      "https://casino.com/deposit",
      0,
      "en"
    );
  }
}
```

---

## Notes

- All monetary values are handled as **integers in cents**
- Always validate callback signatures using the helper methods
- Refer to the Slotsgateway API documentation for detailed behavior and requirements

---

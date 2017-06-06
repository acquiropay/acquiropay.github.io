---
title: PHP
category: SDK
order: 1
---

## Общая информация

Для работы с платежным шлюзом используется библиотека [Omnipay](http://omnipay.thephpleague.com/).

С помощью [Composer](http://getcomposer.org) установите AcquiroPay SDK:

```bash
composer require acquiropay/omnipay-acquiropay
```

## Инициализация шлюза

```php
use Omnipay\Omnipay;

$gateway = Omnipay::create('AcquiroPay');
$gateway->setMerchantId('563');
$gateway->setProductId('4345');
$gateway->setSecretWord('sw');
```

## Примеры использования

#### Двухстадийное списание

После ввода карточных данных клиентом, торговец создает запрос авторизации и переадресовывает клиента на страницу ввода кода 3D Secure:

```
$card = new CreditCard(array(
    'firstName' => 'VASYA',
    'lastName' => 'PUPKIN',
    'number' => '5543735484626654',
    'expiryMonth' => 1,
    'expiryYear' => '2020',
    'cvv' => 362,
));

$request = $gateway->authorize([
    'transactionId' => time(),
    'clientIp' => $_SERVER['REMOTE_ADDR'],
    'amount' => '10.00',
    'card' => $card,
    'returnUrl' => 'http://merchant-site.app/complete-authorization.php',
]);

if (!$response->isSuccessful()) {
    // списание не удалось
}

if ($response->isRedirect()) {
    $_SESSION['transactionReference'] = $response->getTransactionReference();
    $response->getRedirectResponse()->send();
}
``` 

Omnipay сам генерирует код для переадресации и после прохождения вернет пользователя на `returnUrl`.

Далее требуется отправка данных полученных в `returnUrl` в платежный шлюз для подтверждения оплаты:

```php
$request = $gateway->completeAuthorize([
    'transactionReference' => $transactionReference,
    'MD' => $_REQUEST['MD'],
    'PaRes' => $_REQUEST['PaRes'],
]);

$response = $request->send();

if (!$response->isSuccessful()) {
    // холдирование средств не удалось
}
```

На данный момент у клиента средства захолдированы и для полного списания требуется выполнить метод `capture`:

```php
$request = $gateway->capture([
    'transactionReference' => $transactionReference,
]);

$response = $request->send();

if (!$response->isSuccessful()) {
    // списание не удалось
}

// средства успешно списаны
```


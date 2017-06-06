---
title: PHP
category: SDK
order: 1
---

## Общая информация

Для работы с платежным шлюзом используется библиотека [Omnipay](http://omnipay.thephpleague.com/).

## Установка

С помощью [Composer](http://getcomposer.org) установите AcquiroPay SDK:

```bash
composer require acquiropay/omnipay-acquiropay
```

Так же доступно демонстрационное приложение, в котором можно посмотреть как использовать Omnipay вместе с AcquiroPay:

```bash
git clone https://github.com/acquiropay/omnipay-demo
cd omnipay-demo
composer install
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

##### Авторизационный платеж
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

// требуется 3DS авторизация
if ($response->isRedirect()) {
    $_SESSION['transactionReference'] = $response->getTransactionReference();
    $response->redirect();
}
else {
    // выполняем capture
}
``` 

Если `isRedirect` возвращает `true`, Omnipay сам генерирует код для переадресации и после прохождения вернет пользователя на `returnUrl`.

Если `isRedirect` возвращает `false`, минуя "Завершение авторизации" следует выполнить "Списание средств".

##### Завершение авторизации

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

##### Списание средств

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



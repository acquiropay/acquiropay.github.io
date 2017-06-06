---
title: Завершение авторизации
category: Платежный шлюз
order: 3
---

## Параметры запроса

#### Основные параметры

| Название   | Тип     | Обязательность | Описание                                                                 |
|------------|---------|----------------|--------------------------------------------------------------------------|
| opcode     | integer | Да             | Для завершения авторизации - 3.                                          |
| payment_id | string  | Да             | Идентификатор платежа, полученный при авторизации.                       |
| PaRes      | string  | Да             | Ответ от банка-эмитента.                                                 |
| MD         | string  | Да             | Идентификатор 3DS операции банка-экваера.                                |
| token      | string  | Да             | Идентификатор безопасности. MD5 (merchant_id + payment_id + secret_word) |

## Ответ

#### Пример ответа

```xml
<response>
    <payment_id>7d85048f097b4718a12bba8d899c37d8</payment_id>
    <status>OK</status>
    <extended_id>07d47ac11e474448bb735143e84f4562</extended_id>
    <extended_status>PREAUTHORIZATION</extended_status>
    <transaction_status>PREAUTHORIZATION</transaction_status>
    <datetime>2017-06-05 14:02:12+0000</datetime>
    <duplicate>false</duplicate>
    <additional>
        <reestr_id></reestr_id>
        <date_post></date_post>
    </additional>
</response>
```
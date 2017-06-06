---
title: Авторизация платежа
category: Платежный шлюз
order: 2
---

## Параметры запроса

#### Основные параметры

| Название   | Тип     | Обязательность                             | Описание                                                                                                                                                                                                                                                                                                                                                                                                                                                              |   |
|------------|---------|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
| opcode     | integer | Да                                         | Для авторизации платежа - 0.                                                                                                                                                                                                                                                                                                                                                                                                                                          |   |
| product_id | integer | Да                                         | Идентификатор оплачиваемого продукта.                                                                                                                                                                                                                                                                                                                                                                                                                                 |   |
| amount     | float   | Да, если продукт объявлен как "FREE_PRICE" | Сумма к оплате в валюте продукта.  Валюта продукта указывается в настройках продукта.                                                                                                                                                                                                                                                                                                                                                                                 |   |
| cf         | string  | Да                                         | Свободное поле. Поле должно содержать информацию о плательщике.  Например, в поле можно передать номер заказа (номер договора, номер лицевого счета, номер телефона и др.) Если никаких параметров, содержащих информацию о плательщике, нет, то при интеграции необходимо согласовать значение поля с сотрудниками МФР. Записанная информация будет передана обратно Торговцу в callback-ах или при запросе статуса транзакции. Поле cf не может иметь значение «0». |   |
| cf2        | string  | Нет                                        | Свободное поле 2.                                                                                                                                                                                                                                                                                                                                                                                                                                                     |   |
| cf3        | string  | Нет                                        | Свободное поле 2                                                                                                                                                                                                                                                                                                                                                                                                                                                      |   |
| ip_address | string  | Да                                         | Текстовое значение IPv4, например: 80.80.80.80.                                                                                                                                                                                                                                                                                                                                                                                                                       |   |
| cb_url     | String  | Нет                                        | URL для осуществления callback запроса.                                                                                                                                                                                                                                                                                                                                                                                                                               |   |
| token      | string  | Да                                         | Идентификатор безопасности. MD5 (merchant_id + product_Id + amount + cf +cf2 + cf3 + secret_word)                                                                                                                                                                                                                                                                                                                                                                     |   |

#### Параметры при оплате банковской картой

| Название    | Тип     | Обязательность | Описание                                                     |   |
|-------------|---------|----------------|--------------------------------------------------------------|---|
| pan         | integer | Да             | Номер банковской карты.                                      |   |
| cardholder  | string  | Да             | Имя держателя карты.                                         |   |
| exp_month   | string  | Да             | Срок действия банковской карты - месяц. Формат - MM.         |   |
| exp_year    | string  | Да             | Срок действия банковской карты - год. Формат - YYYY.         |   |
| cvv         | int     | Да             | CVC/CVV2 - секретный код банковской карты.                   |   |
| pp_identity | string  | Да             | Идентификатор платежной системы. Для банковских карт - card. |   |

## Ответ

#### Требуется 3DS авторизация

```xml
<response>
    <payment_id>784093adb49a43e9b89a63807d780acb</payment_id>
    <status>3DSECURE</status>
    <extended_id>784093adb49a43e9b89a63807d780acb</extended_id>
    <extended_status>3DSECURE</extended_status
    <transaction_status>3DSECURE</transaction_status>    
    <datetime>2011-04-28 11:52:15.0</datetime>
    <duplicate>false</duplicate>
    <additional>
        <secure3d>
            <auth-form>https://3dsecure.issuerbankdomain.com/sample_acs</auth-form>
            <auth-form-method/>
            <retransmit>
                <TermUrl>...</TermUrl>
                <PaReq>...</PaReq>
                <MD>...</MD>
                <SOME_PARAM_NAME>PARAM_VALUE</SOME_PARAM_NAME>
            </retransmit>
        </secure3d>
    </additional>
</response>
```

#### Возможно списание без 3DS авторизации

```xml
<response>
    <payment_id>b86ec39cc1194ee893df3cea27a98ec9</payment_id>
    <status>OK</status>
    <extended_id>910bcdbb2efa4b9c85ad32fefc0596bf</extended_id>
    <extended_status>PREAUTHORIZATION</extended_status>
    <transaction_status>PREAUTHORIZATION</transaction_status>
    <datetime>2017-06-05 12:53:03+0000</datetime>
    <duplicate>false</duplicate>
    <additional>
        <reestr_id></reestr_id>
        <date_post></date_post>
        <id>b86ec39cc1194ee893df3cea27a98ec9</id>
        <pp_identity>card_visa</pp_identity>
    </additional>
</response>
```


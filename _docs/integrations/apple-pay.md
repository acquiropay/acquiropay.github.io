---
title: Apple Pay
category: Интеграции
order: 1
---

## Шаг 1. Добавление кнопки Apple Pay
    <style>
      #apple-pay-button {
        display: none;
        background-color: black;
        background-image: -webkit-named-image(apple-pay-logo-white);
        background-size: 100% 100%;
        background-origin: content-box;
        background-repeat: no-repeat;
        width: 100%;
        height: 44px;
        padding: 10px 0;
        border-radius: 10px;
      }
    </style>
    <button id="apple-pay-button"></button>
    
## Шаг 2. Инициализации сессии Apple Pay    
    if (window.ApplePaySession) {
         var merchantIdentifier = 'merchant.acquiropay'; // Идентификатор торговца выданный AcquiroPay
         var label = 'Интернет-магазин'; // Название магазина 
         var amount = 100; // Сумма списания
         
         var promise = ApplePaySession.canMakePaymentsWithActiveCard(merchantIdentifier);
    
         promise.then(function(canMakePayments) {
             if (canMakePayments) {
                 document.getElementById('apple-pay-button').addEventListener('click', beginApplePay);
             } else {
                 console.log('ApplePay is possible on this browser, but not currently activated.');
             }
         });
    
         function createRequest(e) {
             const request = {
                 countryCode: 'RU',
                 currencyCode: 'RUB',
                 supportedNetworks: ['visa', 'masterCard'],
                 merchantCapabilities: ['supports3DS'],
                 total: {
                     label: label,
                     amount: amount
                 }
             };
    
             session = new ApplePaySession(1, request);
             session.begin();
    
             console.log('Session started.');
    
             session.oncancel = function(event) {
                 console.log('oncancel');
             };
             session.onvalidatemerchant = validateMerchant;
             session.onpaymentmethodselected = paymentMethodSelected;
             session.onpaymentauthorized = paymentAuthorized;
             session.onshippingcontactselected = function() {
                 console.log('onshippingcontactselected');
             };
             session.onshippingmethodselected = function() {
                 console.log('onshippingmethodselected');
             };
         }
     } 
 
    

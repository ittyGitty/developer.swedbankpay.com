---
title: Swedbank Pay Payments Mobile Pay
sidebar:
  navigation:
  - title: MobilePay Payments
    items:
    - url: /payments/mobile-pay
      title: Introduction
    - url: /payments/mobile-pay/redirect
      title: Redirect
    - url: /payments/mobile-pay/after-payment
      title: After Payment
    - url: /payments/mobile-pay/other-features
      title: Other Features
---

{% include alert-development-section.md %}

{% include jumbotron.html body="**Mobile Pay** is a popular service in Denmark
and Finland. Let **Mobile Pay** be a part of the payment solutions for your
customer." %}

{% include alert.html type="neutral"
                      icon="cached"
                      body="**Redirect** will take the consumer to a Swedbank
                      Pay hosted payment page where they can perform a secure
                      transaction. The consumer will be redirected back to your
                      website after the completion of the payment." %}

## Prerequisites

To start integrating Swedbank Pay Mobile Pay, you need the following:

* [HTTPS][https] enabled web server.
* Agreement that includes Swedbank Pay Mobile Pay.
* Obtained credentials (merchant Access Token) from Swedbank Pay through
  Swedbank Pay Admin.

## Introduction

* When you have prepared your merchant/webshop site, you make a `POST` request
  towards Swedbank Pay with your Purchase information.
  You will receive a Redirect URL, leading to a secure Swedbank Pay hosted
  environment, in response.
* You need to redirect the browser of the end-user/consumer to that URL so
  that she may enter her MobilePay details .
* When the payment is completed, Swedbank Pay will redirect the browser back
  to your merchant/webshop site.
* Finally you need to make a `GET` request towards Swedbank Pay with the
  `paymentID` received in the first step, which will return the purchase result.

![Payex payment menu][mobile-pay-screenshot]

## API requests

The API requests depend on the payment instrument you are using when
implementing the Payment Link scenario, see [purchase flow](#purchase-flow).
One-phase payment metods will not implement capture, cancellation or reversal.
The options you can choose from when creating a payment with key operation set
to Value Purchase are listed below.

## Intent

The intent of the payment identifies how and when the charge will be
effectuated. This determines the type of transaction used during the payment
process.

**Authorization (two-phase)**: The intent of a MobilePay purchase is always
`Authorization`. The amount will be reserved but not charged.
You will later (i.e. if a physical product, when you are ready to ship the
purchased products) have to make a [Capture][mobilepay-capture] or
[Cancel][mobilepay-cancel] request.

## Purchase flow

The sequence diagrams display the high level process of the purchase,
from generating a Payment Link to receving a Callback.

{% include alert.html type="neutral" icon="info" body="
Please note that the the callback may come either before, after or in the same
moment as the consumer are being redirected to the status page at the merchant
site when the purchase is fulfilled." %}

```mermaid
sequenceDiagram
  Consumer->>MerchantOrderSystem: consumer starts purchase
  activate MerchantOrderSystem
  MerchantOrderSystem->>-Merchant: start purchase process
  activate Merchant
  Merchant->>-SwedbankPay: POST [payment] (operation=PURCHASE)
  activate SwedbankPay
  note left of Merchant: First API request
  SwedbankPay-->>-Merchant: payment resource with payment URL
  activate Merchant
  Merchant-->>-MerchantOrderSystem: Payment URL sent to order system
  activate MerchantOrderSystem
  MerchantOrderSystem-->>-Consumer: Distribute Payment URL through e-mail/SMS
  activate Consumer

  note left of Consumer: Payment Link in e-mail/SMS
  Consumer->>-SwedbankPay: Open link and enter payment information
  activate SwedbankPay

  opt Card supports 3-D Secure
  SwedbankPay-->>-Consumer: redirect to IssuingBank
  activate Consumer
  Consumer->>IssuingBank: 3-D Secure authentication process
  Consumer->>-SwedbankPay: access authentication page
  activate SwedbankPay
  end

  SwedbankPay-->>-Consumer: redirect to merchant site
  activate Consumer
  note left of SwedbankPay: redirect back to merchant

  Consumer->>-Merchant: access merchant page
  activate Merchant
  Merchant->>-SwedbankPay: GET [payment]
  activate SwedbankPay
  note left of Merchant: Second API request
  SwedbankPay-->>-Merchant: payment resource
  activate Merchant
  Merchant-->>-Consumer: display purchase result
```

{% include iterator.html
                         next_href="redirect"
                         next_title="Next: Redirect" %}

[mobile-pay-screenshot]: /assets/img/mobilepay-screenshot-1.png
[https]: /#connection-and-protocol
[mobilepay-capture]: /payments/mobile-pay/after-payment#capture
[mobilepay-cancel]: /payments/mobile-pay/after-payment#cancel

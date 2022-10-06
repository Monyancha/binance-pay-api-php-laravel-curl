![Binance Pay API](https://public.bnbstatic.com/image/cms/blog/20211207/273406c6-819c-4a48-a981-8c2dd8b0f907.png)

# Binance Pay API for PHP and Laravel using Curl
Binance Pay API for PHP and Laravel - This is a simple and quick repo on how to initiate crypto payments using the Official Binance API. You can use this to initiate ecommerce payments or any other payments of your choise from your website. 

## Binance Pay API: Authentication
The Binance Pay API uses API keys to authenticate requests. You can view and manage your API keys in the Binance Merchant Admin Portal.

Your API keys carry many privileges, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail.

## Apply API identity key and API secret key#
Register Merchant Account at Binance
Login merchant account and create new API identity key/API secret key [Binance Merchant Admin Portal](https://merchant.binance.com/).


## Create Order
Create order API Version 2 used for merchant/partner to initiate acquiring order.

### Base Url

```
https://bpay.binanceapi.com
```

### Endpoint
```
POST /binancepay/openapi/v2/order
```

### Request Parameters
Check here for all the request parameters
[-> Check here](https://developers.binance.com/docs/binance-pay/api-order-create-v2#request-parameters)

## USAGE IN PHP

```
<?php
    // Generate nonce string
    $chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
    $nonce = '';
    for($i=1; $i <= 32; $i++)
    {
        $pos = mt_rand(0, strlen($chars) - 1);
        $char = $chars[$pos];
        $nonce .= $char;
    }
    $ch = curl_init();
    $timestamp = round(microtime(true) * 1000);
    // Request body
     $request = array(
       "env" => array(
             "terminalType" => "APP" 
          ), 
       "merchantTradeNo" => mt_rand(982538,9825382937292), 
       "orderAmount" => 25.17, 
       "currency" => "BUSD", 
       "goods" => array(
                "goodsType" => "01", 
                "goodsCategory" => "D000", 
                "referenceGoodsId" => "7876763A3B", 
                "goodsName" => "Ice Cream", 
                "goodsDetail" => "Greentea ice cream cone" 
             ) 
    ); 
 
    $json_request = json_encode($request);
    $payload = $timestamp."\n".$nonce."\n".$json_request."\n";
    $binance_pay_key = "REPLACE-WITH-YOUR-BINANCE-MERCHANT-API-KEY";
    $binance_pay_secret = "REPLACE-WITH-YOUR-BINANCE-MERCHANT-SCRETE-KEY";
    $signature = strtoupper(hash_hmac('SHA512',$payload,$binance_pay_secret));
    $headers = array();
    $headers[] = "Content-Type: application/json";
    $headers[] = "BinancePay-Timestamp: $timestamp";
    $headers[] = "BinancePay-Nonce: $nonce";
    $headers[] = "BinancePay-Certificate-SN: $binance_pay_key";
    $headers[] = "BinancePay-Signature: $signature";

    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    curl_setopt($ch, CURLOPT_URL, "https://bpay.binanceapi.com/binancepay/openapi/v2/order");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $json_request);

    $result = curl_exec($ch);
    if (curl_errno($ch)) { echo 'Error:' . curl_error($ch); }
    curl_close ($ch);

    var_dump($result);
    
    //Redirect user to the payment page

?>
```

## USAGE IN LARAVEL

Use this in your targeted controller file.

```
public function initiateBinancePay(){
    // Generate nonce string
    $chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
    $nonce = '';
    for($i=1; $i <= 32; $i++)
    {
        $pos = mt_rand(0, strlen($chars) - 1);
        $char = $chars[$pos];
        $nonce .= $char;
    }
    $ch = curl_init();
    $timestamp = round(microtime(true) * 1000);
    // Request body
     $request = array(
       "env" => array(
             "terminalType" => "APP" 
          ), 
       "merchantTradeNo" => mt_rand(982538,9825382937292), 
       "orderAmount" => 25.17, 
       "currency" => "BUSD", 
       "goods" => array(
                "goodsType" => "01", 
                "goodsCategory" => "D000", 
                "referenceGoodsId" => "7876763A3B", 
                "goodsName" => "Ice Cream", 
                "goodsDetail" => "Greentea ice cream cone" 
             ) 
    ); 
 
    $json_request = json_encode($request);
    $payload = $timestamp."\n".$nonce."\n".$json_request."\n";
    $binance_pay_key = "REPLACE-WITH-YOUR-BINANCE-MERCHANT-API-KEY";
    $binance_pay_secret = "REPLACE-WITH-YOUR-BINANCE-MERCHANT-SCRETE-KEY";
    $signature = strtoupper(hash_hmac('SHA512',$payload,$binance_pay_secret));
    $headers = array();
    $headers[] = "Content-Type: application/json";
    $headers[] = "BinancePay-Timestamp: $timestamp";
    $headers[] = "BinancePay-Nonce: $nonce";
    $headers[] = "BinancePay-Certificate-SN: $binance_pay_key";
    $headers[] = "BinancePay-Signature: $signature";

    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    curl_setopt($ch, CURLOPT_URL, "https://bpay.binanceapi.com/binancepay/openapi/v2/order");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $json_request);

    $result = curl_exec($ch);
    if (curl_errno($ch)) { echo 'Error:' . curl_error($ch); }
    curl_close ($ch);

    var_dump($result);

    //Redirect user to the payment page

}
```

## Sample SUCCESS JSON Response

```
{
  "status": "SUCCESS",
  "code": "000000",
  "data": {
    "prepayId": "29383937493038367292",
    "terminalType": "APP",
    "expireTime": 121123232223,
    "qrcodeLink": "https://qrservice.dev.com/en/qr/dplkb005181944f84b84aba2430e1177012b.jpg",
    "qrContent": "https://qrservice.dev.com/en/qr/dplk12121112b",
    "checkoutUrl": "https://pay.binance.com/checkout/dplk12121112b",
    "deeplink": "bnc://app.binance.com/payment/secpay/xxxxxx",
    "universalUrl": "https://app.binance.com/payment/secpay?_dp=xxx=&linkToken=xxx"
  },
  "errorMessage": ""
}
```

## Binance Pay: Order Webhook Notification
###Callback Webhook Endpoints
Binance Pay will send order events with final status to partner for notification. You will be able to configure webhook endpoints via the Binance Merchant Admin Portal Result of the orders that are close will be notified to you through this webhook with bizStatus = "PAY_CLOSED"

> I'll update a webhook callback usage for this soon! You'll be able to receive the JSON responses and validate payments even storing them to your database


## Upcoming Updates

- [x] #C2B - Create Order/Initiate binance payment :tada:
- [ ] #Receiving webhook responses through callback url for validating and storing response to database
- [ ] #C2C - Customer to Customer crypto funds transfer using binance API
- [ ] #B2C - Business to Customer crypto funds transfer using binance API (Bulk/Batch Payments)
- [ ] #Refunds - Making refunds using the Binance API
- [ ] #Create Sub-merchant API used for merchant/partner.
- [ ] #Wallet Balance Query API used to query wallet balance.
- [ ] #Integrate Binance Pay with your App SDK: Android & IOS

## Binance Pay Official Documentation
https://developers.binance.com/docs/binance-pay/introduction

## Contribution
- Pull requests are welcome.
- Give us a star ⭐
- Fork and Clone! Awesome
- Select existing issues or create a new issue and give us a PR with your bugfix or improvement after. We love it ❤️

> For any inquiries Email: enocmonyancha@gmail.com or Whatsapp +254799117020










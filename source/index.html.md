---
title: Huobi API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://www.huobi.pro/apikey/'>Sign Up for a Huobi API key </a>
  - Login is required for creating an API key

includes:

search: true
---

# Introduction

Welcome to the Huobi API! You can use our API to access all market data, trading, and account management endpoints.

We have language bindings in Shell, and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# General

## Request Format

All API requests are in either GET or POST method. For GET request, all parameters are path parameters. For POST request, all parameters are in POST body and in JSON format.

## Response Format

All response will be returned in JSON format. The top level JSON is a wrapper object which provides three metadata in "status", "ch", and "ts". The actual per API response data is in "data" field.

### Response Wrapper Content

> Response wrapper content example:

```json
{
  "status": "ok",
  "ch": "market.btcusdt.kline.1day",
  "ts": 1499223904680,
  "data": // per API response data in nested JSON object
}
```

Parameter | Data Type | Description
--------- | --------- | -----------
status    | string    | The overall API call result status
ch        | string    | The data channel this response was originated from. Some API return does not have this field.
ts        | int       | The timestamp in milliseconds for when the response is created
data      | object    | The actual response content per API

## Endpoint Rate Limit

Each apikey can send maximum of 100 https requests within 10 seconds. Please contact customer support if you believe you need higher limit rate.

## Authentication

Some API endpoints require authentication. To be authenticated, you should first acquire an API key and the corresponding secret key.

<aside class="notice">
You can manage you API keys by login to your account at huobi.com and go to "API Management" under "Account" section.
</aside>

In order to successfully sign a request, you need to follow below steps

1. Generate the "Query String" for your query

2. Use "Query String" and your secret key to to created a signature

3. Add the signature as a path parameter to your query

### Generate the "Query String" for your query

> Add the query path section of the query string

```shell
[HTTP Method]\n[URL Root]\n[Query Path]\n
```

> For example below

```shell
GET\napi.huobi.pro\n/v1/order/orders\n
```

> Add the authentication section of the query string

```shell
AccessKeyId=[Your API key]&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=[Your Request Timestamp]
```

> For example below

```shell
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2017-05-11T15%3A19%3A30
```

> Add the parameter section of the query string, for example

```shell
&order-id=1234567890
```

> The final query string will be this

```shell
GET\napi.huobi.pro\n/v1/order/orders\nAccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2017-05-11T15%3A19%3A30&order-id=1234567890
```

1. Add the query path to the query string

2. Add mandatory authentication parameters to the query string

3. Add other path parameters to the query string ordered by parameter name (asc)

<aside class="notice">
The timestamp should be in YYYY-MM-DDThh:mm:ss format with URL encoding.
</aside>

### Use "Query String" and your secret key to to created a signature

> The result signature will look like

```shell
4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o=
```

1. Apply HmacSHA256 hash function with inputs (query string, secret key) to get the hashed string

2. Encode the hashed string with base-64

### Add the signature as a path parameter to your query

> The final request with signature will look like

```shell
https://api.huobi.pro/v1/order/orders?AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&order-id=1234567890&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2017-05-11T15%3A19%3A30&Signature=4F65x5A2bLyMWVQj3Aqp%2BB4w%2BivaA7n5Oi2SuYtCJ9o%3D

```

1. Add all mandatory authentication parameters to your path parameter

2. Add "&Signature=[Your request signature with URL encode]" to your path parameter

# Market Data

## Get Klines(candles)

This endpoint retrieves all klines in a specific range.

### HTTP Request

`GET https://api.huobi.pro/market/history/kline`

```shell
curl "https://api.huobi.pro/market/kline?period=1day&size=200&symbol=btcusdt"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1499184000,
    "amount": 37593.0266,
    "count": 0,
    "open": 1935.2000,
    "close": 1879.0000,
    "low": 1856.0000,
    "high": 1940.0000,
    "vol": 71031537.97866500
  }
]
```

### Query Parameters

Parameter | Data Type | Required | Default | Description
--------- | --------- | -------- | ------- | -----------
symbol    | string    | true     | NA      | The trading pair to query, e.g. btcusdt, bccbtc
period    | string    | true     | NA      | The period of each candle, allowed values are: 1min, 5min, 15min, 30min, 60min, 1day, 1mon, 1week, 1year
size      | integer   | false    | 150     | The number of data returns, range [1, 2000]

### Response Content
Parameter | Data Type | Description
--------- | --------- | -----------
id        | integer   | The UNIX timestamp in seconds as response id
amount    | float     | The aggregated trading volume in USDT
count     | integer   | The number of completed trades
open      | float     | The opening price
close     | float     | The closing price
low       | float     | The low price
high      | float     | The high price
vol       | float     | The trading volume in base currency

## Get Latest Aggregated Ticker

This endpoint retrieves the latest ticker with some important 24h aggregated market data.

### HTTP Request

`GET https://api.huobi.pro/market/detail/merged`

```shell
curl "https://api.huobi.pro/market/detail/merged?symbol=ethusdt"
```

> The above command returns JSON structured like this:

```json
{
  "id":1499225271,
  "ts":1499225271000,
  "close":1885.0000,
  "open":1960.0000,
  "high":1985.0000,
  "low":1856.0000,
  "amount":81486.2926,
  "count":42122,
  "vol":157052744.85708200,
  "ask":[1885.0000,21.8804],
  "bid":[1884.0000,1.6702]
}
```

### Query Parameters

Parameter | Data Type | Required | Default | Description
--------- | --------- | -------- | ------- | -----------
symbol    | string    | true     | NA      | The trading pair to query, e.g. btcusdt, bccbtc

### Response Content

Parameter | Data Type | Description
--------- | --------- | -----------
id        | integer   | The UNIX timestamp in seconds as response id
amount    | float     | The aggregated trading volume in USDT
count     | integer   | The number of completed trades
open      | float     | The opening price of last 24 hours
close     | float     | The closing price of last 24 hours
low       | float     | The low price of last 24 hours
high      | float     | The high price of last 24 hours
vol       | float     | The trading volume in base currency of last 24 hours
bid       | object    | The current best bid in format [price, quote volume]
ask       | object    | The current best ask in format [price, quote volume]

## Get Latest Tickers for All Pairs

This endpoint retrieves the latest tickers for all supported pairs.

<aside class="notice">The returned data object can contain large amount of tickers.</aside>

### HTTP Request

`GET https://api.huobi.pro/market/tickers`

```shell
curl "https://api.huobi.pro/market/tickers"
```

> The above command returns JSON structured like this:

```json
[  
    {  
        "open":0.044297,      // daily Kline,opennig price
        "close":0.042178,     // daily Kline,closing price
        "low":0.040110,       // daily Kline,the minimum price
        "high":0.045255,      // daily Kline,the maxmum price
        "amount":12880.8510,  
        "count":12838,
        "vol":563.0388715740,
        "symbol":"ethbtc"
    },
    {  
        "open":0.008545,
        "close":0.008656,
        "low":0.008088,
        "high":0.009388,
        "amount":88056.1860,
        "count":16077,
        "vol":771.7975953754,
        "symbol":"ltcbtc"
    }
]
```

### Query Parameters

This endpoint does not require parameters.

### Response Content

Response content is an array of object, each object has below fields.

Parameter | Data Type | Description
--------- | --------- | -----------
amount    | float     | The aggregated trading volume in USDT of last 24 hours
count     | integer   | The number of completed trades of last 24 hours
open      | float     | The opening price of last 24 hours
close     | float     | The closing price of last 24 hours
low       | float     | The low price of last 24 hours
high      | float     | The high price of last 24 hours
vol       | float     | The trading volume in base currency of last 24 hours
symbol    | string    | The trading pair of this object, e.g. btcusdt, bccbtc

## Get Market Depth

This endpoint retrieves the current order book of a specific pair.

### HTTP Request

`GET https://api.huobi.pro/market/depth`

```shell
curl "https://api.huobi.pro/market/depth?symbol=btcusdt&type=step1"
```

> The above command returns JSON structured like this:

```json
{
    "version": 31615842081,
    "ts": 1489464585407,
    "bids": [
      [7964, 0.0678], // [price, amount]
      [7963, 0.9162],
      [7961, 0.1],
      [7960, 12.8898],
      [7958, 1.2],
      [7955, 2.1009],
      [7954, 0.4708],
      [7953, 0.0564],
      [7951, 2.8031],
      [7950, 13.7785],
      [7949, 0.125],
      [7948, 4],
      [7942, 0.4337],
      [7940, 6.1612],
      [7936, 0.02],
      [7935, 1.3575],
      [7933, 2.002],
      [7932, 1.3449],
      [7930, 10.2974],
      [7929, 3.2226]
    ],
    "asks": [
      [7979, 0.0736],
      [7980, 1.0292],
      [7981, 5.5652],
      [7986, 0.2416],
      [7990, 1.9970],
      [7995, 0.88],
      [7996, 0.0212],
      [8000, 9.2609],
      [8002, 0.02],
      [8008, 1],
      [8010, 0.8735],
      [8011, 2.36],
      [8012, 0.02],
      [8014, 0.1067],
      [8015, 12.9118],
      [8016, 2.5206],
      [8017, 0.0166],
      [8018, 1.3218],
      [8019, 0.01],
      [8020, 13.6584]
    ]
  }
```

### Query Parameters

Parameter | Data Type | Required | Allowed Value                            | Description
--------- | --------- | -------- | -------------                            | -----------
symbol    | string    | true     | All supported trading pair symbols       | The trading pair to query, e.g. btcusdt, bccbtc
type      | string    | true     | step0, step1, step2, step3, step4, step5 | TBC

### Response Content

<aside class="notice">The returned data object is under 'tick' object instead of 'data' object in the top level JSON</aside>

Parameter | Data Type | Description
--------- | --------- | -----------
ts        | integer   | The UNIX timestamp in milliseconds
version   | integer   | TBC
bids      | object    | The current all bids in format [price, quote volume]
asks      | object    | The current all asks in format [price, quote volume]

## Get the Last Trade

This endpoint retrieves the latest trade with its price, volume, and direction.

### HTTP Request

`GET https://api.huobi.pro/market/trade`

```shell
curl "https://api.huobi.pro/market/trade?symbol=ethusdt"
```

> The above command returns JSON structured like this:

```json
{
    "id": 600848670,
    "ts": 1489464451000,
    "data": [
      {
        "id": 600848670,
        "price": 7962.62,
        "amount": 0.0122,
        "direction": "buy",
        "ts": 1489464451000
      }
    ]
}
```

### Query Parameters

Parameter | Data Type | Required | Default | Description
--------- | --------- | -------- | ------- | -----------
symbol    | string    | true     | NA      | The trading pair to query, e.g. btcusdt, bccbtc

### Response Content

<aside class="notice">The returned data object is under 'tick' object instead of 'data' object in the top level JSON</aside>

Parameter | Data Type | Description
--------- | --------- | -----------
id        | integer   | The unique trade id of this trade
amount    | float     | The trading volume in base currency
price     | float     | The trading price in quote currency
ts        | integer   | The UNIX timestamp in milliseconds
direction | string    | The direction of the trade: 'buy' or 'sell'

## Get the Most Recent Trades

This endpoint retrieves the most recent trades with their price, volume, and direction.

### HTTP Request

`GET https://api.huobi.pro/market/history/trade`

```shell
curl "https://api.huobi.pro/market/history/trade?symbol=ethusdt&size=2"
```

> The above command returns JSON structured like this:

```json
[  
   {  
      "id":31618787514,
      "ts":1544390317905,
      "data":[  
         {  
            "amount":9.000000000000000000,
            "ts":1544390317905,
            "id":3161878751418918529341,
            "price":94.690000000000000000,
            "direction":"sell"
         },
         {  
            "amount":73.771000000000000000,
            "ts":1544390317905,
            "id":3161878751418918532514,
            "price":94.660000000000000000,
            "direction":"sell"
         }
      ]
   },
   {  
      "id":31618776989,
      "ts":1544390311353,
      "data":[  
         {  
            "amount":1.000000000000000000,
            "ts":1544390311353,
            "id":3161877698918918522622,
            "price":94.710000000000000000,
            "direction":"buy"
         }
      ]
   }
]
```

### Query Parameters

Parameter | Data Type | Required | Default | Description
--------- | --------- | -------- | ------- | -----------
symbol    | string    | true     | NA      | The trading pair to query, e.g. btcusdt, bccbtc
size      | integer   | false    | 1       | The number of data returns

### Response Content

<aside class="notice">The returned data object is an array represents one recent timestamp; each timestamp object again is an array represents all trades occurred at this timestamp.</aside>

Parameter | Data Type | Description
--------- | --------- | -----------
id        | integer   | The unique trade id of this trade
amount    | float     | The trading volume in base currency
price     | float     | The trading price in quote currency
ts        | integer   | The UNIX timestamp in milliseconds
direction | string    | The direction of the trade: 'buy' or 'sell'

## Get the Last 24h Market Summary

This endpoint retrieves the summary of trading in the market for the last 24 hours.

### HTTP Request

`GET https://api.huobi.pro/market/detail/`

```shell
curl "https://api.huobi.pro/market/detail?symbol=ethusdt"
```

> The above command returns JSON structured like this:

```json
{  
   "amount":613071.438479561,
   "open":86.21,
   "close":94.35,
   "high":98.7,
   "id":31619471534,
   "count":138909,
   "low":84.63,
   "version":31619471534,
   "vol":5.6617373443873316E7
}
```

### Query Parameters

Parameter | Data Type | Required | Default | Description
--------- | --------- | -------- | ------- | -----------
symbol    | string    | true     | NA      | The trading pair to query, e.g. btcusdt, bccbtc

### Response Content

<aside class="notice">The returned data object is under 'tick' object instead of 'data' object in the top level JSON</aside>

Parameter | Data Type | Description
--------- | --------- | -----------
id        | integer   | The UNIX timestamp in seconds as response id
amount    | float     | The aggregated trading volume in USDT
count     | integer   | The number of completed trades
open      | float     | The opening price of last 24 hours
close     | float     | The closing price of last 24 hours
low       | float     | The low price of last 24 hours
high      | float     | The high price of last 24 hours
vol       | float     | The trading volume in base currency of last 24 hours
version   | integer   | TBC

# Spot Trading

<aside class="notice">All endpoints in this section require authentication</aside>

## Place a New Trading Order

This endpoint place an trading order and send to the exchange to be matched.

### HTTP Request

`GET https://api.huobi.pro/v1/order/orders/place`

```shell
curl "https://api.huobi.pro/v1/order/orders/place"
BODY {
   "account-id": "100009",
   "amount": "10.1",
   "price": "100.1",
   "source": "api",
   "symbol": "ethusdt",
   "type": "buy-limit"}
```

> The above command returns JSON structured like this:

```json
{  
  "data": "59378"
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
account-id | string    | true     | NA      | The account id used for this spot trading
symbol     | string    | true     | NA      | The trading pair to trade, e.g. btcusdt, bccbtc
type       | string    | true     | NA      | The order type, possible values are: buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker
amount     | string    | true     | NA      | The amount in quote currency to buy / the amount in base currency to sell
price      | string    | false    | NA      | The limit price of limit order
source     | string    | false    | api     | When trade with margin use 'margin-api'

### Response Content

<aside class="notice">The returned data object is a single string which represents the order id</aside>

## Show All Open Orders

This endpoint returns all open orders which have not been filled completely.

### HTTP Request

`GET https://api.huobi.pro/v1/order/openOrders`

```shell
curl "https://api.huobi.pro/v1/order/openOrders"
BODY {
   "account-id": "100009",
   "amount": "10.1",
   "price": "100.1",
   "source": "api",
   "symbol": "ethusdt",
   "type": "buy-limit"}
```

> The above command returns JSON structured like this:

```json
{  
  "data": [
    {
      "id": 5454937,
      "symbol": "ethusdt",
      "account-id": 30925,
      "amount": "1.000000000000000000",
      "price": "0.453000000000000000",
      "created-at": 1530604762277,
      "type": "sell-limit",
      "filled-amount": "0.0",
      "filled-cash-amount": "0.0",
      "filled-fees": "0.0",
      "source": "web",
      "state": "submitted"
    }
  ]
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
account-id | string    | false    | NA      | The account id used for this spot trading
symbol     | string    | false    | NA      | The trading pair to trade, e.g. btcusdt, bccbtc
side       | string    | false    | both    | Filter on the direction of the trade, possible values are: buy, sell. Default is to return all
size       | int       | false    | 10      | The max number of orders to return

### Response Content

Parameter           | Data Type | Description
---------           | --------- | -----------
id                  | integer   | order id
symbol              | string    | The trading pair to trade, e.g. btcusdt, bccbtc
price               | string    | The limit price of limit order
created-at          | int       | The timestamp in milliseconds when the order was created
type                | string    | The order type, possible values are: buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker
filled-amount       | string    | The amount which has been filled
filled-cash-amount  | string    | The filled total in quote currency
filled-fees         | string    | Transaction fee paid so far
source              | string    | The source where the order was triggered, possible values: sys, web, api, app
state               | string    | submitted, partical-filled, cancelling

## Submit Cancel for an Order

This endpoint submit a request to cancel an order.

<aside class="warning">This only submit the cancel request, the actual result of the canel request needs to be checked by order status or match result endpoints</aside>

### HTTP Request

`POST https://api.huobi.pro/v1/order/orders/{order-id}/submitcancel`

```shell
curl "https://api.huobi.pro/v1/order/orders/59378/submitcancel"
```

> The above command returns JSON structured like this:

```json
{  
  "data": "59378"
}
```

## Submit Cancel for Multiple Orders at Once

This endpoint submit cancellation for multiple orders at once.

### HTTP Request

`GET https://api.huobi.pro/v1/order/orders/batchcancel`

```shell
curl "https://api.huobi.pro/v1/order/orders/batchcancel"
BODY {
  "order-ids": [
    "1", "2", "3"
  ]
}
```

> The above command returns JSON structured like this:

```json
{  
  "data": {
    "success": [
      "1",
      "3"
    ],
    "failed": [
      {
        "err-msg": "记录无效",
        "order-id": "2",
        "err-code": "base-record-invalid"
      }
    ]
  }
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
order-ids  | list      | true     | NA      | The order ids to cancel. Max size is 50.

### Response Content

Parameter           | Data Type | Description
---------           | --------- | -----------
success             | list      | The order ids with thier cancel request sent successfully
failed              | list      | The details of the failed cancel request

## Submit Cancel for Multiple Orders at Once

This endpoint submit cancellation for multiple orders at once.

### HTTP Request

`GET https://api.huobi.pro/v1/order/orders/batchcancel`

```shell
curl "https://api.huobi.pro/v1/order/orders/batchcancel"
BODY {
  "order-ids": [
    "1", "2", "3"
  ]
}
```

> The above command returns JSON structured like this:

```json
{  
  "data": {
    "success": [
      "1",
      "3"
    ],
    "failed": [
      {
        "err-msg": "记录无效",
        "order-id": "2",
        "err-code": "base-record-invalid"
      }
    ]
  }
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
order-ids  | list      | true     | NA      | The order ids to cancel. Max size is 50.

### Response Content

Parameter           | Data Type | Description
---------           | --------- | -----------
success             | list      | The order ids with thier cancel request sent successfully
failed              | list      | The details of the failed cancel request

## Show the Order Detail of One Order

This endpoint returns the detail of one order.

### HTTP Request

`GET https://api.huobi.pro/v1/order/orders/{order-id}`

```shell
curl "https://api.huobi.pro/v1/order/orders/59378"
```

> The above command returns JSON structured like this:

```json
{  
  "data": {
    "id": 59378,
    "symbol": "ethusdt",
    "account-id": 100009,
    "amount": "10.1000000000",
    "price": "100.1000000000",
    "created-at": 1494901162595,
    "type": "buy-limit",
    "field-amount": "10.1000000000",
    "field-cash-amount": "1011.0100000000",
    "field-fees": "0.0202000000",
    "finished-at": 1494901400468,
    "user-id": 1000,
    "source": "api",
    "state": "filled",
    "canceled-at": 0,
    "exchange": "huobi",
    "batch": ""
  }
}
```

### Response Content

Parameter           | Data Type | Description
---------           | --------- | -----------
id                  | integer   | order id
symbol              | string    | The trading pair to trade, e.g. btcusdt, bccbtc
account-id          | string    | The account id which this order belongs to
amount              | string    | The amount of base currency in this order
price               | string    | The limit price of limit order
created-at          | int       | The timestamp in milliseconds when the order was created
finished-at         | int       | The timestamp in milliseconds when the order was changed to a final state. This is not the time the order is matched.
canceled-at         | int       | The timestamp in milliseconds when the order was canceled, if not canceled then has value of 0
type                | string    | The order type, possible values are: buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker
filled-amount       | string    | The amount which has been filled
filled-cash-amount  | string    | The filled total in quote currency
filled-fees         | string    | Transaction fee paid so far
source              | string    | The source where the order was triggered, possible values: sys, web, api, app
state               | string    | Order state: submitted, partical-filled, cancelling, filled, canceled
exchange            | string    | Internal data
batch               | string    | Internal data

## Show the Match Result of an Order

This endpoint returns the match result of an order.

### HTTP Request

`GET https://api.huobi.pro/v1/order/orders/{order-id}/matchresult`

```shell
curl "https://api.huobi.pro/v1/order/orders/59378/matchresult"
```

> The above command returns JSON structured like this:

```json
{  
  "data": [
    {
      "id": 29553,
      "order-id": 59378,
      "match-id": 59335,
      "symbol": "ethusdt",
      "type": "buy-limit",
      "source": "api",
      "price": "100.1000000000",
      "filled-amount": "9.1155000000",
      "filled-fees": "0.0182310000",
      "created-at": 1494901400435
    }
  ]
}
```

### Response Content

<aside class="notice">The return data contains a list and each item in the list represents a match result</aside>

Parameter           | Data Type | Description
---------           | --------- | -----------
id                  | integer   | Internal id
symbol              | string    | The trading pair to trade, e.g. btcusdt, bccbtc
order-id            | string    | The order id of this order
match-id            | string    | The match id of this match
price               | string    | The limit price of limit order
created-at          | int       | The timestamp in milliseconds when the match and fill is done
type                | string    | The order type, possible values are: buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker
filled-amount       | string    | The amount which has been filled
filled-fees         | string    | Transaction fee paid so far
source              | string    | The source where the order was triggered, possible values: sys, web, api, app

## Search Past Orders

This endpoint returns orders based on a specific searching criteria.

### HTTP Request

`GET https://api.huobi.pro/v1/order/orders`

```shell
curl "https://api.huobi.pro/v1/order/orders"
BODY {
   "account-id": "100009",
   "amount": "10.1",
   "price": "100.1",
   "source": "api",
   "symbol": "ethusdt",
   "type": "buy-limit"
   }
```

> The above command returns JSON structured like this:

```json
{  
  "data": [
    {
      "id": 59378,
      "symbol": "ethusdt",
      "account-id": 100009,
      "amount": "10.1000000000",
      "price": "100.1000000000",
      "created-at": 1494901162595,
      "type": "buy-limit",
      "field-amount": "10.1000000000",
      "field-cash-amount": "1011.0100000000",
      "field-fees": "0.0202000000",
      "finished-at": 1494901400468,
      "user-id": 1000,
      "source": "api",
      "state": "filled",
      "canceled-at": 0,
      "exchange": "huobi",
      "batch": ""
    }
  ]
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
symbol     | string    | true     | NA      | The trading pair to trade, e.g. btcusdt, bccbtc
types      | string    | false    | All     | The types of order to include in the search
states     | string    | false    | All     | The states of order to include in the search
start-date | string    | false    | -61d    | Search starts date, in format yyyy-mm-dd
end-date   | string    | false    | today    | Search ends date, in format yyyy-mm-dd
from       | string    | false    | both    | Search order id to begin with
direct     | string    | false    | both    | Search direction when 'from' is used, possible values: 'next', 'prev'
size       | int       | false    | 100     | The max number of orders to return, max value is 100

### Response Content

Parameter           | Data Type | Description
---------           | --------- | -----------
id                  | integer   | Order id
account-id          | integer   | Account id
user-id             | integer   | User id
amount              | string    | The amount of base currency in this order
symbol              | string    | The trading pair to trade, e.g. btcusdt, bccbtc
price               | string    | The limit price of limit order
created-at          | int       | The timestamp in milliseconds when the order was created
canceled-at         | int       | The timestamp in milliseconds when the order was canceled, or 0 if not canceled
canceled-at         | int       | The timestamp in milliseconds when the order was finished, or 0 if not finished
type                | string    | The order type, possible values are: buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker
filled-amount       | string    | The amount which has been filled
filled-cash-amount  | string    | The filled total in quote currency
filled-fees         | string    | Transaction fee paid so far
source              | string    | The source where the order was triggered, possible values: sys, web, api, app
state               | string    | submitted, partical-filled, cancelling
exchange            | string    | Internal data
batch               | string    | Internal data

## Search the Match Results

This endpoint returns the match results of past and open orders based on specific search criteria.

### HTTP Request

`GET https://api.huobi.pro/v1/order/matchresults`

```shell
curl "https://api.huobi.pro/v1/order/matchresults"
```

> The above command returns JSON structured like this:

```json
{  
  "data": [
    {
      "id": 29553,
      "order-id": 59378,
      "match-id": 59335,
      "symbol": "ethusdt",
      "type": "buy-limit",
      "source": "api",
      "price": "100.1000000000",
      "filled-amount": "9.1155000000",
      "filled-fees": "0.0182310000",
      "created-at": 1494901400435
    }
  ]
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
symbol     | string    | true     | NA      | The trading pair to trade, e.g. btcusdt, bccbtc
types      | string    | false    | All     | The types of order to include in the search
states     | string    | false    | All     | The states of order to include in the search
start-date | string    | false    | -61d    | Search starts date, in format yyyy-mm-dd
end-date   | string    | false    | today   | Search ends date, in format yyyy-mm-dd
from       | string    | false    | NA      | Search match result id to begin with
direct     | string    | false    | next    | Search direction when 'from' is used, possible values: 'next', 'prev'
size       | int       | false    | 100     | The max number of orders to return

### Response Content

<aside class="notice">The return data contains a list and each item in the list represents a match result</aside>

Parameter           | Data Type | Description
---------           | --------- | -----------
id                  | integer   | Internal id
symbol              | string    | The trading pair to trade, e.g. btcusdt, bccbtc
order-id            | string    | The order id of this order
match-id            | string    | The match id of this match
price               | string    | The limit price of limit order
created-at          | int       | The timestamp in milliseconds when the match and fill is done
type                | string    | The order type, possible values are: buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker
filled-amount       | string    | The amount which has been filled
filled-fees         | string    | Transaction fee paid so far
source              | string    | The source where the order was triggered, possible values: sys, web, api, app

# Margin Trading

<aside class="notice">All endpoints in this section require authentication</aside>

<aside class="notice">Currently margin trade only supports base currency of USDT and BTC</aside>

<aside class="warning">All order placed with margin should set the 'source' field to 'margin-api'</aside>

## Transfer Asset from Spot Trading Account to Margin Account

This endpoint transfer specific asset from spot trading account to margin account.

### HTTP Request

`GET https://api.huobi.pro/v1/dw/transfer-in`

```shell
curl "https://api.huobi.pro/v1/dw/transfer-in"
BODY
{
  "symbol": "ethusdt",
  "currency": "eth",
  "amount": "1.0"
}
```

> The above command returns JSON structured like this:

```json
{  
  "data": 1000
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
symbol     | string    | true     | NA      | The trading pair to borrow margin, e.g. btcusdt, bccbtc
currency   | string    | true     | NA      | The currency to borrow
amount     | string    | true     | NA      | The amount of currency to borrow

### Response Content

<aside class="notice">The return data contains a single value instead of an object</aside>

Parameter           | Data Type | Description
---------           | --------- | -----------
data                | integer   | Transfer id

## Transfer Asset from Margin Account to Spot Trading Account

This endpoint transfer specific asset from margin account to spot trading account.

### HTTP Request

`GET https://api.huobi.pro/v1/dw/transfer-out`

```shell
curl "https://api.huobi.pro/v1/dw/transfer-out"
BODY
{
  "symbol": "ethusdt",
  "currency": "eth",
  "amount": "1.0"
}
```

> The above command returns JSON structured like this:

```json
{  
  "data": 1000
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
symbol     | string    | true     | NA      | The trading pair to borrow margin, e.g. btcusdt, bccbtc
currency   | string    | true     | NA      | The currency to borrow
amount     | string    | true     | NA      | The amount of currency to borrow

### Response Content

<aside class="notice">The return data contains a single value instead of an object</aside>

Parameter           | Data Type | Description
---------           | --------- | -----------
data                | integer   | Transfer id

## Place a Margin Loan Order

This endpoint place an order to initiate a margin loan.

### HTTP Request

`GET https://api.huobi.pro/v1/margin/orders`

```shell
curl "https://api.huobi.pro/v1/margin/orders"
BODY
{
  "symbol": "ethusdt",
  "currency": "eth",
  "amount": "1.0"
}
```

> The above command returns JSON structured like this:

```json
{  
  "data": 1000
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
symbol     | string    | true     | NA      | The trading pair to borrow margin, e.g. btcusdt, bccbtc
currency   | string    | true     | NA      | The currency to borrow
amount     | string    | true     | NA      | The amount of currency to borrow

### Response Content

<aside class="notice">The return data contains a single value instead of an object</aside>

Parameter           | Data Type | Description
---------           | --------- | -----------
data                | integer   | Margin order id

## Repay Margin Loan

This endpoint repays margin loan with you asset in your margin account.

### HTTP Request

`POST https://api.huobi.pro/v1/margin/orders/{order-id}/repay`

```shell
curl "https://api.huobi.pro/v1/margin/orders/1000/repay"
BODY
{
  "amount": "1.0"
}
```

> The above command returns JSON structured like this:

```json
{  
  "data": 1000
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
amount     | string    | true     | NA      | The amount of currency to repay

### Response Content

<aside class="notice">The return data contains a single value instead of an object</aside>

Parameter           | Data Type | Description
---------           | --------- | -----------
data                | integer   | Margin order id

## Search Past Margin Orders

This endpoint returns margin orders based on a specific searching criteria.

### HTTP Request

`GET https://api.huobi.pro/v1/margin/loan-orders`

```shell
curl "https://api.huobi.pro/v1/margin/load-orders"
BODY {
   "account-id": "100009",
   "amount": "10.1",
   "price": "100.1",
   "source": "api",
   "symbol": "ethusdt",
   "type": "buy-limit"
   }
```

> The above command returns JSON structured like this:

```json
{  
  "data": [
    {
      "loan-balance": "0.100000000000000000",
      "interest-balance": "0.000200000000000000",
      "interest-rate": "0.002000000000000000",
      "loan-amount": "0.100000000000000000",
      "accrued-at": 1511169724531,
      "interest-amount": "0.000200000000000000",
      "symbol": "ethbtc",
      "currency": "btc",
      "id": 394,
      "state": "accrual",
      "account-id": 17747,
      "user-id": 119913,
      "created-at": 1511169724531
    }
  ]
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
symbol     | string    | true     | NA      | The trading pair to trade, e.g. btcusdt, bccbtc
states     | string    | false    | All     | The states of order to include in the search
start-date | string    | false    | -61d    | Search starts date, in format yyyy-mm-dd
end-date   | string    | false    | today    | Search ends date, in format yyyy-mm-dd
from       | string    | false    | both    | Search order id to begin with
direct     | string    | false    | both    | Search direction when 'from' is used, possible values: 'next', 'prev'
size       | int       | false    | 100     | The max number of orders to return, max value is 100

### Response Content

Parameter           | Data Type | Description
---------           | --------- | -----------
id                  | integer   | Order id
account-id          | integer   | Account id
user-id             | integer   | User id
symbol              | string    | The margin loan pair to trade, e.g. btcusdt, bccbtc
currency            | string    | The currency in the loan
created-at          | int       | The timestamp in milliseconds when the order was created
accrued-at          | int       | The timestamp in milliseconds when the last accure happened
type                | string    | The order type, possible values are: buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc, buy-limit-maker, sell-limit-maker
loan-amount         | string    | The amount of the origin loan
loan-balance        | string    | The amount of the loan left
interest-rate       | string    | The loan interest rate
interest-amount     | string    | The accumulated loan interest
interest-balance    | string    | The amount of loan interest left
state               | string    | Loan state, possible values: created, accrual, cleared, invalid

## Return the Balance of the Margin Loan Account

This endpoint returns the balance of the margin loan account.

### HTTP Request

`GET https://api.huobi.pro/v1/margin/accounts/balance`

```shell
curl "https://api.huobi.pro/v1/margin/accounts/balance"
BODY {
   "account-id": "100009",
   "amount": "10.1",
   "price": "100.1",
   "source": "api",
   "symbol": "ethusdt",
   "type": "buy-limit"
   }
```

> The above command returns JSON structured like this:

```json
{  
"data": [
        {
            "id": 18264,
            "type": "margin",
            "state": "working",
            "symbol": "btcusdt",
            "fl-price": "0",
            "fl-type": "safe",
            "risk-rate": "475.952571086994250554",
            "list": [
                {
                    "currency": "btc",
                    "type": "trade",
                    "balance": "1168.533000000000000000"
                },
                {
                    "currency": "btc",
                    "type": "frozen",
                    "balance": "0.000000000000000000"
                },
                {
                    "currency": "btc",
                    "type": "loan",
                    "balance": "-2.433000000000000000"
                },
                {
                    "currency": "btc",
                    "type": "interest",
                    "balance": "-0.000533000000000000"
                },
                {
                    "currency": "btc",
                    "type": "transfer-out-available",//可转btc
                    "balance": "1163.872174670000000000"
                },
                {
                    "currency": "btc",
                    "type": "loan-available",//可借btc
                    "balance": "8161.876538350676000000"
                }
            ]
        }
    ]
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
symbol     | string    | true     | NA      | The trading pair to borrow margin, e.g. btcusdt, bccbtc

### Response Content

Parameter           | Data Type     | Description
---------           | ---------     | -----------
symbol              | string        | The margin loan pair, e.g. btcusdt, bccbtc
state               | string        | Balance state, possible values: working,fl-sys,fl-mgt,fl-end
risk-rate           | string        | The risk rate
fl-price            | string        | The price which triggers closeout
list                | object array  | The list of loans and their details

# Wallet (Deposit and Withdraw)

## Create a Withdraw Request

This endpoint creates a withdraw request from your spot trading account to an external address.

<aside class="notice">Only supported the existed addresses in your withdraw address list</aside>

### HTTP Request

`GET https://api.huobi.pro/v1/dw/withdraw/api/create`

```shell
curl "https://api.huobi.pro/v1/dw/withdraw/api/create"
BODY
{
  "address": "0xde709f2102306220921060314715629080e2fb77",
  "amount": "0.05",
  "currency": "eth",
  "fee": "0.01"
}
```

> The above command returns JSON structured like this:

```json
{  
  "data": 1000
}
```

### Query Parameters

Parameter  | Data Type | Required | Default | Description
---------  | --------- | -------- | ------- | -----------
address    | string    | true     | NA      | The desination address of this withdraw
currency   | string    | true     | NA      | The crypto currency to withdraw
amount     | string    | true     | NA      | The amount of currency to withdraw
fee        | string    | true     | NA      | The trading pair to borrow margin, e.g. btcusdt, bccbtc
addr-tag   | string    | true     | NA      | The trading pair to borrow margin, e.g. btcusdt, bccbtc

### Response Content

<aside class="notice">The return data contains a single value instead of an object</aside>

Parameter           | Data Type | Description
---------           | --------- | -----------
data                | integer   | Transfer id

TBC

# Account

TBC

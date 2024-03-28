# Evo Exchange API Documentation

## Overview
This document outlines the **Evo Exchange API** endpoint details necessary for the market makers. The market makers can manage their orders by **placing**, **updating**, **cancelling**, and **fetching** orders.

Base URL: https://api.evo.exchange

## Authentication
The Evo Exchange API uses API keys for authentication. Include your API key in the request headers as follows:
```
X-EVOX_API_KEY: YOUR_API_KEY
```

## Endpoints
### Place a trade
```bash
POST /order
```

Place an order and execute a trade if it matches orders in the book.

**Request Params**
- `id` (string, required): The id of the base currency (smart contract address).

**Request Body**
- `order_size` (string, required): The amount of order (string number).
- `order_price` (string, required): The price of order (string number).
- `order_side` (string, required): The side of order (buy or sell).
- `order_variant` (string, required): The variant of order (market or limit).
- `address` (string, required): The taker wallet address.
- `margined` (bool, required): The margin status of order (default: false).

**Example Request**
```bash
POST /order?id=0x1E67a46D59527B8a77D1eC7C6EEc0B06FcF31E28
Content-Type: application/json
X-EVOX_API_KEY: YOUR_API_KEY

{
    "order_size": "100",
    "order_price": "3.45",
    "order_side": "BUY",
    "order_variant": "MARKET",
    "address": "0x19E75eD87d138B18263AfE40f7C16E4a5ceCB585",
    "margined": false
}
```

**Response**

Status Code: `200`
- `_tradeid` (string): The id of trade.
- `token_1` (string): The address of base currency.
- `token_2` (string): The address of target currency.
- `buyers` (array): The array of buyers' addresses.
- `sellers` (array): The array of sellers' addresses.
- `buy_amnts` (array): The array of trade amounts in target currency.
- `sell_amnts` (array): The array of trade amounts in base currency.

Status Code: `400` and `403`
- `error` (string): The error message.

**Example Response**

Status Code: `200`
```bash
{
    "_tradeid": "6605a79b61d879dcefc2da48",
    "token_1": "0x1E67a46D59527B8a77D1eC7C6EEc0B06FcF31E28",
    "token_2": "0xaBAD60e4e01547E2975a96426399a5a0578223Cb",
    "buyers": ["0xa60c6a918502174CA68EB335Ae28471103460783"],
    "sellers": ["0x92F1bDc41B8E7863459DA1cD6Fc8A00aa69CcbF6"],
    "buy_amnts": ["33300000000000000000"],
    "sell_amnts": ["10000000000000000000"]
}
```

Status Code: `400` and `403`
```bash
{
    "error": "The order price is missing or is not valid."
}
```

### Place an order in the book only
```bash
POST /order/place
```

Place only trades that go to the book, if they are matching trades it cancels the trade completely. Purpose of this is for market makers.

**Request Params**
- `id` (string, required): The id of the base currency (smart contract address).

**Request Body**
- `order_size` (string, required): The amount of order (string number).
- `order_price` (string, required): The price of order (string number).
- `order_side` (string, required): The side of order (buy or sell).
- `address` (string, required): The taker wallet address.
- `original_id` (string, optional): This field is useful when you want to update the existing order with new amount and price.

**Example Request**
```bash
POST /order/place?id=0x1E67a46D59527B8a77D1eC7C6EEc0B06FcF31E28
Content-Type: application/json
X-EVOX_API_KEY: YOUR_API_KEY

{
    "order_size": "100",
    "order_price": "3.45",
    "order_side": "BUY",
    "address": "0x19E75eD87d138B18263AfE40f7C16E4a5ceCB585",
    "original_id": "65fb894ce33fb1bb634592d1"
}
```

**Response**

Status Code: `200`
- `msg` (string): The success message.
- `data` (string): The id of newly placed order.

Status Code: `400` and `403`
- `error` (string): The error message.

**Example Response**

Status Code: `200`
```bash
{
    "msg": "Success",
    "data": "65fb894ce33fb1bb634592d1"
}
```

Status Code: `400` and `403`
```bash
{
    "error": "The order price is missing or is not valid."
}
```

### Fetch order book details
```bash
GET /order
```

Retrieve a list of order book details.

**Request Params**
- id (string, required): The id of the base currency (smart contract address).
- address (string, optional): The wallet address used for filtering.

**Example Request**
```bash
GET /order?id=0x1E67a46D59527B8a77D1eC7C6EEc0B06FcF31E28&address=0x19E75eD87d138B18263AfE40f7C16E4a5ceCB585
Content-Type: application/json
X-EVOX_API_KEY: YOUR_API_KEY
```

**Response**

An array filled with order book details.

**Example Response**
```bash
[
    {
        "_id": {
            "$oid": "66037c0c5a6cf015284d56c2"
        },
        "order_id": 0,
        "order_size": "1",
        "address": "0x400DF2b3Cc8702C5b3260171Eb2742d695A0a841",
        "order_price": "3.3217",
        "asset_type": "[]",
        "order_variant": "LIMIT",
        "order_side": "BUY",
        "margined": false,
        "timestamp": "1711504396",
        "filled_size": "0",
        "filled_part": [],
        "symbol": "EVOX",
        "token_address": "0x1E67a46D59527B8a77D1eC7C6EEc0B06FcF31E28"
    },
    ...
]
```

### Cancel an order from the book
```bash
POST /order/
```

Cancel the specific order from the book.

**Request Params**
- `id` (string, required): The id of the base currency (smart contract address).

**Request Body**
- `_id` (string, required): The document id of an order to be canceled.
- `order_variant` (string, required): The variant of order (must be "CANCELORDER").

**Example Request**
```bash
POST /order?id=0x1E67a46D59527B8a77D1eC7C6EEc0B06FcF31E28
Content-Type: application/json
X-EVOX_API_KEY: YOUR_API_KEY

{
    "_id": "65fdb2e4a24776bf729088f4",
    "order_variant": "CANCELORDER",
}
```

**Response**

Status Code: `200`
An array filled with updated orders.

Status Code: `400` and `404`
- `error` (string): The error message.

**Example Response**

Status Code: `200`
```bash
[
    {
        "_id": {
            "$oid": "66037c0c5a6cf015284d56c2"
        },
        "order_id": 0,
        "order_size": "1",
        "address": "0x400DF2b3Cc8702C5b3260171Eb2742d695A0a841",
        "order_price": "3.3217",
        "asset_type": "[]",
        "order_variant": "LIMIT",
        "order_side": "BUY",
        "margined": false,
        "timestamp": "1711504396",
        "filled_size": "0",
        "filled_part": [],
        "symbol": "EVOX",
        "token_address": "0x1E67a46D59527B8a77D1eC7C6EEc0B06FcF31E28"
    },
    ...
]
```

Status Code: `400` and `404`
```bash
{
    "error": "The order id is missing."
}
```
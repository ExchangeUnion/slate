---
title: Xud API Reference

language_tabs:
  - shell
  - python
  - javascript

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---
# Introduction
```javascript
var fs = require('fs');
var grpc = require('grpc');
var options = {
  convertFieldsToCamelCase: true,
  longsAsStrings: true,
};
var xudProto = grpc.load('xudrpc.proto', 'proto', options);
var tlsCert = fs.readFileSync('path/to/tls.cert');
var sslCreds = grpc.credentials.createSsl(tlsCert);
var xudClient = new xudProto.Xud('localhost:8886', sslCreds);
```
```python
# Python requires you to generate static protobuf code, see the following guide:
# https://grpc.io/docs/tutorials/basic/python.html#generating-client-and-server-code

import grpc
import xudrpc_pb2 as xud, xudrpc_pb2_grpc as xudrpc
cert = open('path/to/tls.cert', 'rb').read()
ssl_creds = grpc.ssl_channel_credentials(cert)
channel = grpc.secure_channel('localhost:8886', ssl_creds)
xud_stub = xudrpc.XudStub(channel)
```
This is the API documentation for the Xud gRPC service.
Xud is a decentralized exchange built on the Lightning and Raiden networks to enable instant and trustless cryptocurrency swaps and order fulfillment between cryptocurrency exchanges. Exchanges participating in the network aggregate their liquidity and can provide deeper order books and new trading pairs to their users.
# RPC Calls
## AddCurrency
```javascript
var request = {
  currency: <string>,
  swapClient: <SwapClient>,
  tokenAddress: <string>,
  decimalPlaces: <uint32>,
};
xudClient.addCurrency(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.AddCurrencyRequest(
  currency=<string>,
  swap_client=<SwapClient>,
  token_address=<string>,
  decimal_places=<uint32>,
)
response = xudStub.AddCurrency(request)
print(response)
# Output: {}
```
```shell
xucli addcurrency <currency> <swap_client> [decimal_places] [token_address]
```
Adds a currency to the list of supported currencies. Once added, the currency may be used for new trading pairs.
### Request
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol for this currency such as BTC, LTC, ETH, etc...
swap_client | [SwapClient](#swapclient) | The payment channel network client to use for executing swaps.
token_address | string | The contract address for layered tokens such as ERC20.
decimal_places | uint32 | The number of places to the right of the decimal point of the smallest subunit of the currency. For example, BTC, LTC, and others where the smallest subunits (satoshis) are 0.00000001 full units (bitcoins) have 8 decimal places. ETH has 18. This can be thought of as the base 10 exponent of the smallest subunit expressed as a positive integer. A default value of 8 is used if unspecified.
### Response
This response has no parameters.
## AddPair
```javascript
var request = {
  baseCurrency: <string>,
  quoteCurrency: <string>,
};
xudClient.addPair(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.AddPairRequest(
  base_currency=<string>,
  quote_currency=<string>,
)
response = xudStub.AddPair(request)
print(response)
# Output: {}
```
```shell
xucli addpair <base_currency> <quote_currency>
```
Adds a trading pair to the list of supported trading pairs. The newly supported pair is advertised to peers so they may begin sending orders for it.
### Request
Parameter | Type | Description
--------- | ---- | -----------
base_currency | string | The base currency that is bought and sold for this trading pair.
quote_currency | string | The currency used to quote a price for the base currency.
### Response
This response has no parameters.
## Ban
```javascript
var request = {
  nodePubKey: <string>,
};
xudClient.ban(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.BanRequest(
  node_pub_key=<string>,
)
response = xudStub.Ban(request)
print(response)
# Output: {}
```
```shell
xucli ban <node_pub_key>
```
Bans a node and immediately disconnects from it. This can be used to prevent any connections to a specific node.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_pub_key | string | The node pub key of the node to ban.
### Response
This response has no parameters.
## ChannelBalance
```javascript
var request = {
  currency: <string>,
};
xudClient.channelBalance(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "balances": <ChannelBalance>
// }
```
```python
request = xud.ChannelBalanceRequest(
  currency=<string>,
)
response = xudStub.ChannelBalance(request)
print(response)
# Output:
# {
#  "balances": <ChannelBalance>
# }
```
```shell
xucli channelbalance [currency]
```
Gets the total balance available across all payment channels for one or all currencies.
### Request
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to query for, if unspecified then balances for all supported currencies are queried.
### Response
Parameter | Type | Description
--------- | ---- | -----------
balances | map&lt;string, [ChannelBalance](#channelbalance)&gt; | A map between currency ticker symbols and their channel balances.
## Connect
```javascript
var request = {
  nodeUri: <string>,
};
xudClient.connect(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.ConnectRequest(
  node_uri=<string>,
)
response = xudStub.Connect(request)
print(response)
# Output: {}
```
```shell
xucli connect <node_uri>
```
Attempts to connect to a node. Once connected, the node is added to the list of peers and becomes available for swaps and trading. A handshake exchanges information about the peer's supported trading and swap clients. Orders will be shared with the peer upon connection and upon new order placements.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_uri | string | The uri of the node to connect to in "[nodePubKey]@[host]:[port]" format.
### Response
This response has no parameters.
## ExecuteSwap
```javascript
var request = {
  orderId: <string>,
  pairId: <string>,
  peerPubKey: <string>,
  quantity: <uint64>,
};
xudClient.executeSwap(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "orderId": <string>,
//  "localId": <string>,
//  "pairId": <string>,
//  "quantity": <uint64>,
//  "rHash": <string>,
//  "amountReceived": <int64>,
//  "amountSent": <int64>,
//  "peerPubKey": <string>,
//  "role": <Role>,
//  "currencyReceived": <string>,
//  "currencySent": <string>,
//  "rPreimage": <string>,
//  "price": <double>
// }
```
```python
request = xud.ExecuteSwapRequest(
  order_id=<string>,
  pair_id=<string>,
  peer_pub_key=<string>,
  quantity=<uint64>,
)
response = xudStub.ExecuteSwap(request)
print(response)
# Output:
# {
#  "order_id": <string>,
#  "local_id": <string>,
#  "pair_id": <string>,
#  "quantity": <uint64>,
#  "r_hash": <string>,
#  "amount_received": <int64>,
#  "amount_sent": <int64>,
#  "peer_pub_key": <string>,
#  "role": <Role>,
#  "currency_received": <string>,
#  "currency_sent": <string>,
#  "r_preimage": <string>,
#  "price": <double>
# }
```
```shell
xucli executeswap <pair_id> <order_id> [quantity]
```
Executes a swap on a maker peer order.
### Request
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The order id of the maker order.
pair_id | string | The trading pair of the swap orders.
peer_pub_key | string | The node pub key of the peer which owns the maker order. This is optional but helps locate the order more quickly.
quantity | uint64 | The quantity to swap. The whole order will be swapped if unspecified.
### Response
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that was swapped.
local_id | string | The local id for the order that was swapped.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was swapped.
r_hash | string | The hex-encoded payment hash for the swaps.
amount_received | int64 | The amount of the smallest base unit of the currency (like satoshis or wei) received.
amount_sent | int64 | The amount of the smallest base unit of the currency (like satoshis or wei) sent.
peer_pub_key | string | The node pub key of the peer that executed this order.
role | [Role](#role) | Our role in the swap, either MAKER or TAKER.
currency_received | string | The ticker symbol of the currency received.
currency_sent | string | The ticker symbol of the currency sent.
r_preimage | string | The hex-encoded preimage.
price | double | The price used for the swap.
## GetInfo
```javascript
var request = {};
xudClient.getInfo(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "version": <string>,
//  "nodePubKey": <string>,
//  "uris": <string[]>,
//  "numPeers": <int32>,
//  "numPairs": <int32>,
//  "orders": <OrdersCount>,
//  "lnd": <LndInfo>,
//  "raiden": <RaidenInfo>
// }
```
```python
request = xud.GetInfoRequest()
response = xudStub.GetInfo(request)
print(response)
# Output:
# {
#  "version": <string>,
#  "node_pub_key": <string>,
#  "uris": <string[]>,
#  "num_peers": <int32>,
#  "num_pairs": <int32>,
#  "orders": <OrdersCount>,
#  "lnd": <LndInfo>,
#  "raiden": <RaidenInfo>
# }
```
```shell
xucli getinfo
```
Gets general information about this node.
### Request
This request has no parameters.
### Response
Parameter | Type | Description
--------- | ---- | -----------
version | string | The version of this instance of xud.
node_pub_key | string | The node pub key of this node.
uris | string array | A list of uris that can be used to connect to this node. These are shared with peers.
num_peers | int32 | The number of currently connected peers.
num_pairs | int32 | The number of supported trading pairs.
orders | [OrdersCount](#orderscount) | The number of active, standing orders in the order book.
lnd | map&lt;string, [LndInfo](#lndinfo)&gt; |
raiden | [RaidenInfo](#raideninfo) |
## GetNodeInfo
```javascript
var request = {
  nodePubKey: <string>,
};
xudClient.getNodeInfo(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "reputationScore": <int32>,
//  "banned": <bool>
// }
```
```python
request = xud.GetNodeInfoRequest(
  node_pub_key=<string>,
)
response = xudStub.GetNodeInfo(request)
print(response)
# Output:
# {
#  "reputationScore": <int32>,
#  "banned": <bool>
# }
```
```shell
xucli getnodeinfo <node_pub_key>
```
Gets general information about a node.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_pub_key | string | The node pub key of the node for which to get information.
### Response
Parameter | Type | Description
--------- | ---- | -----------
reputationScore | int32 | The node's reputation score. Points are subtracted for unexpected or potentially malicious behavior. Points are added when swaps are successfully executed.
banned | bool | Whether the node is currently banned.
## ListOrders
```javascript
var request = {
  pairId: <string>,
  includeOwnOrders: <bool>,
};
xudClient.listOrders(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "orders": <Orders>
// }
```
```python
request = xud.ListOrdersRequest(
  pair_id=<string>,
  include_own_orders=<bool>,
)
response = xudStub.ListOrders(request)
print(response)
# Output:
# {
#  "orders": <Orders>
# }
```
```shell
xucli listorders [pair_id]
```
Gets orders from the order book. This call returns the state of the order book at a given point in time, although it is not guaranteed to still be vaild by the time a response is received and processed by a client. It accepts an optional trading pair id parameter. If specified, only orders for that particular trading pair are returned. Otherwise, all orders are returned. Orders are separated into buys and sells for each trading pair, but unsorted.
### Request
Parameter | Type | Description
--------- | ---- | -----------
pair_id | string | The trading pair for which to retrieve orders.
include_own_orders | bool | Whether own orders should be included in result or not.
### Response
Parameter | Type | Description
--------- | ---- | -----------
orders | map&lt;string, [Orders](#orders)&gt; | A map between pair ids and their buy and sell orders.
## ListCurrencies
```javascript
var request = {};
xudClient.listCurrencies(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "currencies": <string[]>
// }
```
```python
request = xud.ListCurrenciesRequest()
response = xudStub.ListCurrencies(request)
print(response)
# Output:
# {
#  "currencies": <string[]>
# }
```
Gets a list of this node's supported currencies.
### Request
This request has no parameters.
### Response
Parameter | Type | Description
--------- | ---- | -----------
currencies | string array | A list of ticker symbols of the supported currencies.
## ListPairs
```javascript
var request = {};
xudClient.listPairs(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "pairs": <string[]>
// }
```
```python
request = xud.ListPairsRequest()
response = xudStub.ListPairs(request)
print(response)
# Output:
# {
#  "pairs": <string[]>
# }
```
```shell
xucli listpairs
```
Gets a list of this nodes suported trading pairs.
### Request
This request has no parameters.
### Response
Parameter | Type | Description
--------- | ---- | -----------
pairs | string array | The list of supported trading pair tickers in formats like "LTC/BTC".
## ListPeers
```javascript
var request = {};
xudClient.listPeers(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "peers": <Peer[]>
// }
```
```python
request = xud.ListPeersRequest()
response = xudStub.ListPeers(request)
print(response)
# Output:
# {
#  "peers": <Peer[]>
# }
```
```shell
xucli listpeers
```
Gets a list of connected peers.
### Request
This request has no parameters.
### Response
Parameter | Type | Description
--------- | ---- | -----------
peers | [Peer](#peer) array | The list of connected peers.
## PlaceOrder
```javascript
var request = {
  price: <double>,
  quantity: <uint64>,
  pairId: <string>,
  orderId: <string>,
  side: <OrderSide>,
};
var call = xudClient.placeOrder(request);
call.on('data', function (response) {
  console.log(response);
});
call.on('error', function (err) {
  console.error(err);
});
call.on('end', function () {
  // the streaming call has been ended by the server
});
// Output:
// {
//  "internalMatch": <Order>,
//  "swapSuccess": <SwapSuccess>,
//  "remainingOrder": <Order>,
//  "swapFailure": <SwapFailure>
// }
```
```python
request = xud.PlaceOrderRequest(
  price=<double>,
  quantity=<uint64>,
  pair_id=<string>,
  order_id=<string>,
  side=<OrderSide>,
)
for response in stub.PlaceOrder(request):
  print(response)
# Output:
# {
#  "internal_match": <Order>,
#  "swap_success": <SwapSuccess>,
#  "remaining_order": <Order>,
#  "swap_failure": <SwapFailure>
# }
```
Adds an order to the order book. If price is zero or unspecified a market order will get added.
### Request
Parameter | Type | Description
--------- | ---- | -----------
price | double | The price of the order.
quantity | uint64 | The quantity of the order in satoshis.
pair_id | string | The trading pair that the order is for.
order_id | string | The local id to assign to the order.
side | [OrderSide](#orderside) | Whether the order is a Buy or Sell.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
internal_match | [Order](#order) | An own order (or portion thereof) that matched the newly placed order.
swap_success | [SwapSuccess](#swapsuccess) | A successful swap of a peer order that matched the newly placed order.
remaining_order | [Order](#order) | The remaining portion of the order, after matches, that enters the order book.
swap_failure | [SwapFailure](#swapfailure) | A swap attempt that failed.
## PlaceOrderSync
```javascript
var request = {
  price: <double>,
  quantity: <uint64>,
  pairId: <string>,
  orderId: <string>,
  side: <OrderSide>,
};
xudClient.placeOrderSync(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "internalMatches": <Order[]>,
//  "swapSuccesses": <SwapSuccess[]>,
//  "remainingOrder": <Order>,
//  "swapFailures": <SwapFailure[]>
// }
```
```python
request = xud.PlaceOrderRequest(
  price=<double>,
  quantity=<uint64>,
  pair_id=<string>,
  order_id=<string>,
  side=<OrderSide>,
)
response = xudStub.PlaceOrderSync(request)
print(response)
# Output:
# {
#  "internal_matches": <Order[]>,
#  "swap_successes": <SwapSuccess[]>,
#  "remaining_order": <Order>,
#  "swap_failures": <SwapFailure[]>
# }
```
```shell
xucli buy <quantity> <pair_id> <price> [order_id] [stream]
xucli sell <quantity> <pair_id> <price> [order_id] [stream]
```
The synchronous, non-streaming version of PlaceOrder.
### Request
Parameter | Type | Description
--------- | ---- | -----------
price | double | The price of the order.
quantity | uint64 | The quantity of the order in satoshis.
pair_id | string | The trading pair that the order is for.
order_id | string | The local id to assign to the order.
side | [OrderSide](#orderside) | Whether the order is a Buy or Sell.
### Response
Parameter | Type | Description
--------- | ---- | -----------
internal_matches | [Order](#order) array | A list of own orders (or portions thereof) that matched the newly placed order.
swap_successes | [SwapSuccess](#swapsuccess) array | A list of successful swaps of peer orders that matched the newly placed order.
remaining_order | [Order](#order) | The remaining portion of the order, after matches, that enters the order book.
swap_failures | [SwapFailure](#swapfailure) array | A list of swap attempts that failed.
## RemoveCurrency
```javascript
var request = {
  currency: <string>,
};
xudClient.removeCurrency(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.RemoveCurrencyRequest(
  currency=<string>,
)
response = xudStub.RemoveCurrency(request)
print(response)
# Output: {}
```
```shell
 xucli removecurrency <currency>
```
Removes a currency from the list of supported currencies. Only currencies that are not in use for any currently supported trading pairs may be removed. Once removed, the currency can no longer be used for any supported trading pairs.
### Request
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol for this currency such as BTC, LTC, ETH, etc...
### Response
This response has no parameters.
## RemoveOrder
```javascript
var request = {
  orderId: <string>,
  quantity: <uint64>,
};
xudClient.removeOrder(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "quantityOnHold": <uint64>
// }
```
```python
request = xud.RemoveOrderRequest(
  order_id=<string>,
  quantity=<uint64>,
)
response = xudStub.RemoveOrder(request)
print(response)
# Output:
# {
#  "quantity_on_hold": <uint64>
# }
```
```shell
xucli removeorder <order_id> [quantity]
```
Removes an order from the order book by its local id. This should be called when an order is canceled or filled outside of xud. Removed orders become immediately unavailable for swaps, and peers are notified that the order is no longer valid. Any portion of the order that is on hold due to ongoing swaps will not be removed until after the swap attempts complete.
### Request
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The local id of the order to remove.
quantity | uint64 | The quantity to remove from the order. If zero or unspecified then entire order is removed.
### Response
Parameter | Type | Description
--------- | ---- | -----------
quantity_on_hold | uint64 | Any portion of the order that was on hold due to ongoing swaps at the time of the request and could not be removed until after the swaps finish.
## RemovePair
```javascript
var request = {
  pairId: <string>,
};
xudClient.removePair(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.RemovePairRequest(
  pair_id=<string>,
)
response = xudStub.RemovePair(request)
print(response)
# Output: {}
```
```shell
xucli removepair <pair_id>
```
Removes a trading pair from the list of currently supported trading pair. This call will effectively cancel any standing orders for that trading pair. Peers are informed when a pair is no longer supported so that they will know to stop sending orders for it.
### Request
Parameter | Type | Description
--------- | ---- | -----------
pair_id | string | The trading pair ticker to remove in a format such as "LTC/BTC".
### Response
This response has no parameters.
## Shutdown
```javascript
var request = {};
xudClient.shutdown(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.ShutdownRequest()
response = xudStub.Shutdown(request)
print(response)
# Output: {}
```
```shell
xucli shutdown
```
Begin gracefully shutting down xud.
### Request
This request has no parameters.
### Response
This response has no parameters.
## SubscribeAddedOrders
```javascript
var request = {
  existing: <bool>,
};
var call = xudClient.subscribeAddedOrders(request);
call.on('data', function (response) {
  console.log(response);
});
call.on('error', function (err) {
  console.error(err);
});
call.on('end', function () {
  // the streaming call has been ended by the server
});
// Output:
// {
//  "price": <double>,
//  "quantity": <uint64>,
//  "pairId": <string>,
//  "id": <string>,
//  "peerPubKey": <string>,
//  "localId": <string>,
//  "createdAt": <int64>,
//  "side": <OrderSide>,
//  "isOwnOrder": <bool>,
//  "hold": <uint64>
// }
```
```python
request = xud.SubscribeAddedOrdersRequest(
  existing=<bool>,
)
for response in stub.SubscribeAddedOrders(request):
  print(response)
# Output:
# {
#  "price": <double>,
#  "quantity": <uint64>,
#  "pair_id": <string>,
#  "id": <string>,
#  "peer_pub_key": <string>,
#  "local_id": <string>,
#  "created_at": <int64>,
#  "side": <OrderSide>,
#  "is_own_order": <bool>,
#  "hold": <uint64>
# }
```
Subscribes to orders being added to the order book. This call, together with SubscribeRemovedOrders, allows the client to maintain an up-to-date view of the order book. For example, an exchange that wants to show its users a real time list of the orders available to them would subscribe to this streaming call to be alerted of new orders as they become available for trading.
### Request
Parameter | Type | Description
--------- | ---- | -----------
existing | bool | Whether to transmit all existing active orders upon establishing the stream.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
price | double | The price of the order.
quantity | uint64 | The quantity of the order in satoshis.
pair_id | string | The trading pair that this order is for.
id | string | A UUID for this order.
peer_pub_key | string | The node pub key of the peer that created this order.
local_id | string | The local id for this order.
created_at | int64 | The epoch time when this order was created.
side | [OrderSide](#orderside) | Whether this order is a buy or sell
is_own_order | bool | Whether this order is a local own order or a remote peer order.
hold | uint64 | The quantity on hold pending swap exectuion.
## SubscribeRemovedOrders
```javascript
var request = {};
var call = xudClient.subscribeRemovedOrders(request);
call.on('data', function (response) {
  console.log(response);
});
call.on('error', function (err) {
  console.error(err);
});
call.on('end', function () {
  // the streaming call has been ended by the server
});
// Output:
// {
//  "quantity": <uint64>,
//  "pairId": <string>,
//  "orderId": <string>,
//  "localId": <string>,
//  "isOwnOrder": <bool>
// }
```
```python
request = xud.SubscribeRemovedOrdersRequest()
for response in stub.SubscribeRemovedOrders(request):
  print(response)
# Output:
# {
#  "quantity": <uint64>,
#  "pair_id": <string>,
#  "order_id": <string>,
#  "local_id": <string>,
#  "is_own_order": <bool>
# }
```
Subscribes to orders being removed - either in full or in part - from the order book. This call, together with SubscribeAddedOrders, allows the client to maintain an up-to-date view of the order book. For example, an exchange that wants to show its users a real time list of the orders available to them would subscribe to this streaming call to be alerted when part or all of an existing order is no longer available for trading.
### Request
This request has no parameters.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
quantity | uint64 | The quantity of the order being removed.
pair_id | string | The trading pair that the order is for.
order_id | string | The global UUID for the order.
local_id | string | The local id for the order, if applicable.
is_own_order | bool | Whether the order being removed is a local own order or a remote peer order.
## SubscribeSwaps
```javascript
var request = {
  includeTaker: <bool>,
};
var call = xudClient.subscribeSwaps(request);
call.on('data', function (response) {
  console.log(response);
});
call.on('error', function (err) {
  console.error(err);
});
call.on('end', function () {
  // the streaming call has been ended by the server
});
// Output:
// {
//  "orderId": <string>,
//  "localId": <string>,
//  "pairId": <string>,
//  "quantity": <uint64>,
//  "rHash": <string>,
//  "amountReceived": <int64>,
//  "amountSent": <int64>,
//  "peerPubKey": <string>,
//  "role": <Role>,
//  "currencyReceived": <string>,
//  "currencySent": <string>,
//  "rPreimage": <string>,
//  "price": <double>
// }
```
```python
request = xud.SubscribeSwapsRequest(
  include_taker=<bool>,
)
for response in stub.SubscribeSwaps(request):
  print(response)
# Output:
# {
#  "order_id": <string>,
#  "local_id": <string>,
#  "pair_id": <string>,
#  "quantity": <uint64>,
#  "r_hash": <string>,
#  "amount_received": <int64>,
#  "amount_sent": <int64>,
#  "peer_pub_key": <string>,
#  "role": <Role>,
#  "currency_received": <string>,
#  "currency_sent": <string>,
#  "r_preimage": <string>,
#  "price": <double>
# }
```
Subscribes to completed swaps. By default, only swaps that are initiated by a remote peer are transmitted unless a flag is set to include swaps initiated by the local node. This call allows the client to get real-time notifications when its orders are filled by a peer. It can be used for tracking order executions, updating balances, and informing a trader when one of their orders is settled through the Exchange Union network.
### Request
Parameter | Type | Description
--------- | ---- | -----------
include_taker | bool | Whether to include the results for swaps initiated via the PlaceOrder or ExecuteSwap calls. These swap results are also returned in the responses for the respective calls.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that was swapped.
local_id | string | The local id for the order that was swapped.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was swapped.
r_hash | string | The hex-encoded payment hash for the swaps.
amount_received | int64 | The amount of the smallest base unit of the currency (like satoshis or wei) received.
amount_sent | int64 | The amount of the smallest base unit of the currency (like satoshis or wei) sent.
peer_pub_key | string | The node pub key of the peer that executed this order.
role | [Role](#role) | Our role in the swap, either MAKER or TAKER.
currency_received | string | The ticker symbol of the currency received.
currency_sent | string | The ticker symbol of the currency sent.
r_preimage | string | The hex-encoded preimage.
price | double | The price used for the swap.
## SubscribeSwapFailures
```javascript
var request = {
  includeTaker: <bool>,
};
var call = xudClient.subscribeSwapFailures(request);
call.on('data', function (response) {
  console.log(response);
});
call.on('error', function (err) {
  console.error(err);
});
call.on('end', function () {
  // the streaming call has been ended by the server
});
// Output:
// {
//  "orderId": <string>,
//  "pairId": <string>,
//  "quantity": <uint64>,
//  "peerPubKey": <string>,
//  "failureReason": <string>
// }
```
```python
request = xud.SubscribeSwapsRequest(
  include_taker=<bool>,
)
for response in stub.SubscribeSwapFailures(request):
  print(response)
# Output:
# {
#  "order_id": <string>,
#  "pair_id": <string>,
#  "quantity": <uint64>,
#  "peer_pub_key": <string>,
#  "failure_reason": <string>
# }
```
Subscribes to failed swaps. By default, only swaps that are initiated by a remote peer are transmitted unless a flag is set to include swaps initiated by the local node. This call allows the client to get real-time notifications when swap attempts are failing. It can be used for status monitoring, debugging, and testing purposes.
### Request
Parameter | Type | Description
--------- | ---- | -----------
include_taker | bool | Whether to include the results for swaps initiated via the PlaceOrder or ExecuteSwap calls. These swap results are also returned in the responses for the respective calls.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that failed the swap.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was attempted to be swapped.
peer_pub_key | string | The node pub key of the peer that we attempted to swap with.
failure_reason | string | The reason why the swap failed.
## Unban
```javascript
var request = {
  nodePubKey: <string>,
  reconnect: <bool>,
};
xudClient.unban(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.UnbanRequest(
  node_pub_key=<string>,
  reconnect=<bool>,
)
response = xudStub.Unban(request)
print(response)
# Output: {}
```
```shell
xucli unban <node_pub_key> [reconnect]
```
Removes a ban from a node manually and, optionally, attempts to connect to it.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_pub_key | string | The node pub key of the peer to unban.
reconnect | bool | Whether to attempt to connect to the peer after it is unbanned.
### Response
This response has no parameters.
# Messages
## AddCurrencyRequest
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol for this currency such as BTC, LTC, ETH, etc...
swap_client | [SwapClient](#swapclient) | The payment channel network client to use for executing swaps.
token_address | string | The contract address for layered tokens such as ERC20.
decimal_places | uint32 | The number of places to the right of the decimal point of the smallest subunit of the currency. For example, BTC, LTC, and others where the smallest subunits (satoshis) are 0.00000001 full units (bitcoins) have 8 decimal places. ETH has 18. This can be thought of as the base 10 exponent of the smallest subunit expressed as a positive integer. A default value of 8 is used if unspecified.
## AddCurrencyResponse
This message has no parameters.
## AddPairRequest
Parameter | Type | Description
--------- | ---- | -----------
base_currency | string | The base currency that is bought and sold for this trading pair.
quote_currency | string | The currency used to quote a price for the base currency.
## AddPairResponse
This message has no parameters.
## BanRequest
Parameter | Type | Description
--------- | ---- | -----------
node_pub_key | string | The node pub key of the node to ban.
## BanResponse
This message has no parameters.
## ChannelBalance
Parameter | Type | Description
--------- | ---- | -----------
balance | int64 | Sum of channels balances denominated in satoshis or equivalent.
pending_open_balance | int64 | Sum of channels pending balances denominated in satoshis or equivalent.
## ChannelBalanceRequest
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to query for, if unspecified then balances for all supported currencies are queried.
## ChannelBalanceResponse
Parameter | Type | Description
--------- | ---- | -----------
balances | map&lt;string, [ChannelBalance](#channelbalance)&gt; | A map between currency ticker symbols and their channel balances.
## ConnectRequest
Parameter | Type | Description
--------- | ---- | -----------
node_uri | string | The uri of the node to connect to in "[nodePubKey]@[host]:[port]" format.
## ConnectResponse
This message has no parameters.
## ExecuteSwapRequest
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The order id of the maker order.
pair_id | string | The trading pair of the swap orders.
peer_pub_key | string | The node pub key of the peer which owns the maker order. This is optional but helps locate the order more quickly.
quantity | uint64 | The quantity to swap. The whole order will be swapped if unspecified.
## SwapFailure
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that failed the swap.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was attempted to be swapped.
peer_pub_key | string | The node pub key of the peer that we attempted to swap with.
failure_reason | string | The reason why the swap failed.
## GetInfoRequest
This message has no parameters.
## GetInfoResponse
Parameter | Type | Description
--------- | ---- | -----------
version | string | The version of this instance of xud.
node_pub_key | string | The node pub key of this node.
uris | string array | A list of uris that can be used to connect to this node. These are shared with peers.
num_peers | int32 | The number of currently connected peers.
num_pairs | int32 | The number of supported trading pairs.
orders | [OrdersCount](#orderscount) | The number of active, standing orders in the order book.
lnd | map&lt;string, [LndInfo](#lndinfo)&gt; |
raiden | [RaidenInfo](#raideninfo) |
## GetNodeInfoRequest
Parameter | Type | Description
--------- | ---- | -----------
node_pub_key | string | The node pub key of the node for which to get information.
## GetNodeInfoResponse
Parameter | Type | Description
--------- | ---- | -----------
reputationScore | int32 | The node's reputation score. Points are subtracted for unexpected or potentially malicious behavior. Points are added when swaps are successfully executed.
banned | bool | Whether the node is currently banned.
## ListOrdersRequest
Parameter | Type | Description
--------- | ---- | -----------
pair_id | string | The trading pair for which to retrieve orders.
include_own_orders | bool | Whether own orders should be included in result or not.
## ListOrdersResponse
Parameter | Type | Description
--------- | ---- | -----------
orders | map&lt;string, [Orders](#orders)&gt; | A map between pair ids and their buy and sell orders.
## ListCurrenciesRequest
This message has no parameters.
## ListCurrenciesResponse
Parameter | Type | Description
--------- | ---- | -----------
currencies | string array | A list of ticker symbols of the supported currencies.
## ListPairsRequest
This message has no parameters.
## ListPairsResponse
Parameter | Type | Description
--------- | ---- | -----------
pairs | string array | The list of supported trading pair tickers in formats like "LTC/BTC".
## ListPeersRequest
This message has no parameters.
## ListPeersResponse
Parameter | Type | Description
--------- | ---- | -----------
peers | [Peer](#peer) array | The list of connected peers.
## LndChannels
Parameter | Type | Description
--------- | ---- | -----------
active | int32 | The number of active/online channels for this lnd instance that can be used for swaps.
inactive | int32 | The number of inactive/offline channels for this lnd instance.
pending | int32 | The number of channels that are pending on-chain confirmation before they can be used.
## LndInfo
Parameter | Type | Description
--------- | ---- | -----------
error | string |
channels | [LndChannels](#lndchannels) |
chains | string array |
blockheight | int32 |
uris | string array |
version | string |
alias | string |
## Order
Parameter | Type | Description
--------- | ---- | -----------
price | double | The price of the order.
quantity | uint64 | The quantity of the order in satoshis.
pair_id | string | The trading pair that this order is for.
id | string | A UUID for this order.
peer_pub_key | string | The node pub key of the peer that created this order.
local_id | string | The local id for this order.
created_at | int64 | The epoch time when this order was created.
side | [OrderSide](#orderside) | Whether this order is a buy or sell
is_own_order | bool | Whether this order is a local own order or a remote peer order.
hold | uint64 | The quantity on hold pending swap exectuion.
## OrderRemoval
Parameter | Type | Description
--------- | ---- | -----------
quantity | uint64 | The quantity of the order being removed.
pair_id | string | The trading pair that the order is for.
order_id | string | The global UUID for the order.
local_id | string | The local id for the order, if applicable.
is_own_order | bool | Whether the order being removed is a local own order or a remote peer order.
## Orders
Parameter | Type | Description
--------- | ---- | -----------
buy_orders | [Order](#order) array | A list of buy orders sorted by descending price.
sell_orders | [Order](#order) array | A list of sell orders sorted by ascending price.
## OrdersCount
Parameter | Type | Description
--------- | ---- | -----------
peer | int32 | The number of orders belonging to remote xud nodes.
own | int32 | The number of orders belonging to our local xud node.
## Peer
Parameter | Type | Description
--------- | ---- | -----------
address | string | The socket address with host and port for this peer.
node_pub_key | string | The node pub key to uniquely identify this peer.
lnd_pub_keys | map&lt;string, string&gt; | A map of ticker symbols to lnd pub keys for this peer
inbound | bool | Indicates whether this peer was connected inbound.
pairs | string array | A list of trading pair tickers supported by this peer.
xud_version | string | The version of xud being used by the peer.
seconds_connected | int32 | The time in seconds that we have been connected to this peer.
raiden_address | string | The raiden address for this peer
## PlaceOrderRequest
Parameter | Type | Description
--------- | ---- | -----------
price | double | The price of the order.
quantity | uint64 | The quantity of the order in satoshis.
pair_id | string | The trading pair that the order is for.
order_id | string | The local id to assign to the order.
side | [OrderSide](#orderside) | Whether the order is a Buy or Sell.
## PlaceOrderResponse
Parameter | Type | Description
--------- | ---- | -----------
internal_matches | [Order](#order) array | A list of own orders (or portions thereof) that matched the newly placed order.
swap_successes | [SwapSuccess](#swapsuccess) array | A list of successful swaps of peer orders that matched the newly placed order.
remaining_order | [Order](#order) | The remaining portion of the order, after matches, that enters the order book.
swap_failures | [SwapFailure](#swapfailure) array | A list of swap attempts that failed.
## PlaceOrderEvent
Parameter | Type | Description
--------- | ---- | -----------
internal_match | [Order](#order) | An own order (or portion thereof) that matched the newly placed order.
swap_success | [SwapSuccess](#swapsuccess) | A successful swap of a peer order that matched the newly placed order.
remaining_order | [Order](#order) | The remaining portion of the order, after matches, that enters the order book.
swap_failure | [SwapFailure](#swapfailure) | A swap attempt that failed.
## RaidenInfo
Parameter | Type | Description
--------- | ---- | -----------
error | string |
address | string |
channels | int32 |
version | string |
## RemoveCurrencyRequest
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol for this currency such as BTC, LTC, ETH, etc...
## RemoveCurrencyResponse
This message has no parameters.
## RemoveOrderRequest
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The local id of the order to remove.
quantity | uint64 | The quantity to remove from the order. If zero or unspecified then entire order is removed.
## RemoveOrderResponse
Parameter | Type | Description
--------- | ---- | -----------
quantity_on_hold | uint64 | Any portion of the order that was on hold due to ongoing swaps at the time of the request and could not be removed until after the swaps finish.
## RemovePairRequest
Parameter | Type | Description
--------- | ---- | -----------
pair_id | string | The trading pair ticker to remove in a format such as "LTC/BTC".
## RemovePairResponse
This message has no parameters.
## ShutdownRequest
This message has no parameters.
## ShutdownResponse
This message has no parameters.
## SubscribeAddedOrdersRequest
Parameter | Type | Description
--------- | ---- | -----------
existing | bool | Whether to transmit all existing active orders upon establishing the stream.
## SubscribeRemovedOrdersRequest
This message has no parameters.
## SubscribeSwapsRequest
Parameter | Type | Description
--------- | ---- | -----------
include_taker | bool | Whether to include the results for swaps initiated via the PlaceOrder or ExecuteSwap calls. These swap results are also returned in the responses for the respective calls.
## SwapSuccess
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that was swapped.
local_id | string | The local id for the order that was swapped.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was swapped.
r_hash | string | The hex-encoded payment hash for the swaps.
amount_received | int64 | The amount of the smallest base unit of the currency (like satoshis or wei) received.
amount_sent | int64 | The amount of the smallest base unit of the currency (like satoshis or wei) sent.
peer_pub_key | string | The node pub key of the peer that executed this order.
role | [Role](#role) | Our role in the swap, either MAKER or TAKER.
currency_received | string | The ticker symbol of the currency received.
currency_sent | string | The ticker symbol of the currency sent.
r_preimage | string | The hex-encoded preimage.
price | double | The price used for the swap.
## UnbanRequest
Parameter | Type | Description
--------- | ---- | -----------
node_pub_key | string | The node pub key of the peer to unban.
reconnect | bool | Whether to attempt to connect to the peer after it is unbanned.
## UnbanResponse
This message has no parameters.
# Enums
## SwapClient
Enumeration | Value | Description
----------- | ----- | -----------
LND | 0 |
RAIDEN | 1 |
## OrderSide
Enumeration | Value | Description
----------- | ----- | -----------
BUY | 0 |
SELL | 1 |
## Role
Enumeration | Value | Description
----------- | ----- | -----------
TAKER | 0 |
MAKER | 1 |

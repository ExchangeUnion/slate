---
title: API Reference

language_tabs:
  - shell
  - javascript
  - python

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---
# XudInit Service
```javascript
var fs = require('fs');
var grpc = require('grpc');
var options = {
  convertFieldsToCamelCase: true,
  longsAsStrings: true,
};
var xudinitProto = grpc.load('xudrpc.proto', 'proto', options);
var tlsCert = fs.readFileSync('path/to/tls.cert');
var sslCreds = grpc.credentials.createSsl(tlsCert);
var xudinitClient = new xudinitProto.XudInit(host + ':' + port, sslCreds);
```
```python
# Python requires you to generate static protobuf code, see the following guide:
# https://grpc.io/docs/tutorials/basic/python.html#generating-client-and-server-code

import grpc
import xudrpc_pb2 as xudinit, xudrpc_pb2_grpc as xudinitrpc
cert = open('path/to/tls.cert', 'rb').read()
ssl_creds = grpc.ssl_channel_credentials(cert)
channel = grpc.secure_channel(host + ':' + port, ssl_creds)
xudinit_stub = xudrpc.XudInitStub(channel)
```
A service for interacting with a locked or uninitalized xud node.
## CreateNode
```javascript
var request = {
  password: <string>,
};

xudinitClient.createNode(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "seedMnemonic": <string[]>,
//  "initializedLnds": <string[]>,
//  "initializedConnext": <bool>
// }
```
```python
request = xudinit.CreateNodeRequest(
  password=<string>,
)
response = xudinitStub.CreateNode(request)
print(response)
# Output:
# {
#  "seed_mnemonic": <string[]>,
#  "initialized_lnds": <string[]>,
#  "initialized_connext": <bool>
# }
```
```shell
  xucli create
  ```
Creates an xud identity node key and underlying wallets. The node key and wallets are derived from a single seed and encrypted using a single password provided as a parameter to the call.
### Request
Parameter | Type | Description
--------- | ---- | -----------
password | string | The password in utf-8 with which to encrypt the new xud node key as well as any uninitialized underlying wallets.
### Response
Parameter | Type | Description
--------- | ---- | -----------
seed_mnemonic | string array | The 24 word mnemonic to recover the xud identity key and underlying wallets
initialized_lnds | string array | The list of lnd clients that were initialized.
initialized_connext | bool | Whether the connext wallet was initialized.
## RestoreNode
```javascript
var request = {
  seedMnemonic: <string[]>,
  password: <string>,
  lndBackups: <bytes>,
  xudDatabase: <bytes>,
};

xudinitClient.restoreNode(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "restoredLnds": <string[]>,
//  "restoredConnext": <bool>
// }
```
```python
request = xudinit.RestoreNodeRequest(
  seed_mnemonic=<string[]>,
  password=<string>,
  lnd_backups=<bytes>,
  xud_database=<bytes>,
)
response = xudinitStub.RestoreNode(request)
print(response)
# Output:
# {
#  "restored_lnds": <string[]>,
#  "restored_connext": <bool>
# }
```
```shell
  xucli restore [backup_directory]
  ```
Restores an xud instance and underlying wallets from a seed.
### Request
Parameter | Type | Description
--------- | ---- | -----------
seed_mnemonic | string array | The 24 word mnemonic to recover the xud identity key and underlying wallets
password | string | The password in utf-8 with which to encrypt the restored xud node key as well as any restored underlying wallets.
lnd_backups | map&lt;string, bytes&gt; | A map between the currency of the LND and its multi channel SCB
xud_database | bytes | The XUD database backup
### Response
Parameter | Type | Description
--------- | ---- | -----------
restored_lnds | string array | The list of lnd clients that were initialized.
restored_connext | bool | Whether the connext wallet was initialized.
## UnlockNode
```javascript
var request = {
  password: <string>,
};

xudinitClient.unlockNode(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "unlockedLnds": <string[]>,
//  "lockedLnds": <string[]>
// }
```
```python
request = xudinit.UnlockNodeRequest(
  password=<string>,
)
response = xudinitStub.UnlockNode(request)
print(response)
# Output:
# {
#  "unlocked_lnds": <string[]>,
#  "locked_lnds": <string[]>
# }
```
```shell
  xucli unlock
  ```
Unlocks and decrypts the xud node key and any underlying wallets.
### Request
Parameter | Type | Description
--------- | ---- | -----------
password | string | The password in utf-8 with which to unlock an existing xud node key as well as underlying client wallets such as lnd.
### Response
Parameter | Type | Description
--------- | ---- | -----------
unlocked_lnds | string array | The list of lnd clients that were unlocked.
locked_lnds | string array | The list of lnd clients that could not be unlocked.
# Xud Service
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
var xudClient = new xudProto.Xud(host + ':' + port, sslCreds);
```
```python
# Python requires you to generate static protobuf code, see the following guide:
# https://grpc.io/docs/tutorials/basic/python.html#generating-client-and-server-code

import grpc
import xudrpc_pb2 as xud, xudrpc_pb2_grpc as xudrpc
cert = open('path/to/tls.cert', 'rb').read()
ssl_creds = grpc.ssl_channel_credentials(cert)
channel = grpc.secure_channel(host + ':' + port, ssl_creds)
xud_stub = xudrpc.XudStub(channel)
```
The primary service for interacting with a running xud node.
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
request = xud.Currency(
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
  nodeIdentifier: <string>,
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
  node_identifier=<string>,
)
response = xudStub.Ban(request)
print(response)
# Output: {}
```
```shell
  xucli ban <node_identifier>
  ```
Bans a node and immediately disconnects from it. This can be used to prevent any connections to a specific node.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the node to ban.
### Response
This response has no parameters.
## ChangePassword
```javascript
var request = {
  newPassword: <string>,
  oldPassword: <string>,
};

xudClient.changePassword(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.ChangePasswordRequest(
  new_password=<string>,
  old_password=<string>,
)
response = xudStub.ChangePassword(request)
print(response)
# Output: {}
```
```shell
  xucli changepass
  ```
Changes the xud master password, including the wallet passwords for any underlying clients.
### Request
Parameter | Type | Description
--------- | ---- | -----------
new_password | string | 
old_password | string | 
### Response
This response has no parameters.
## CloseChannel
```javascript
var request = {
  nodeIdentifier: <string>,
  currency: <string>,
  force: <bool>,
  destination: <string>,
  amount: <uint64>,
  fee: <uint64>,
};

xudClient.closeChannel(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "transactionIds": <string[]>
// }
```
```python
request = xud.CloseChannelRequest(
  node_identifier=<string>,
  currency=<string>,
  force=<bool>,
  destination=<string>,
  amount=<uint64>,
  fee=<uint64>,
)
response = xudStub.CloseChannel(request)
print(response)
# Output:
# {
#  "transaction_ids": <string[]>
# }
```
```shell
  xucli closechannel <currency> [node_identifier ] [--force]
  ```
Closes any existing payment channels with a peer for the specified currency.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer with which to close any channels with.
currency | string | The ticker symbol of the currency of the channel to close.
force | bool | Whether to force close the channel in case the peer is offline or unresponsive.
destination | string | The on-chain address to send funds extracted from the channel. If unspecified, the funds return to the default wallet for the client closing the channel.
amount | uint64 | For Connext only - the amount to extract from the channel. If 0 or unspecified, the entire off-chain balance for the specified currency will be extracted.
fee | uint64 | A manual fee rate set in sat/byte that should be used when crafting the closure transaction.
### Response
Parameter | Type | Description
--------- | ---- | -----------
transaction_ids | string array | The id of the transaction per channel close.
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
## WalletDeposit
```javascript
var request = {
  currency: <string>,
};

xudClient.walletDeposit(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "address": <string>
// }
```
```python
request = xud.DepositRequest(
  currency=<string>,
)
response = xudStub.WalletDeposit(request)
print(response)
# Output:
# {
#  "address": <string>
# }
```
```shell
  xucli walletdeposit <currency>
  ```
Gets an address to deposit a given currency into the xud wallets.
### Request
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to deposit.
### Response
Parameter | Type | Description
--------- | ---- | -----------
address | string | The address to use to deposit funds.
## DiscoverNodes
```javascript
var request = {
  nodeIdentifier: <string>,
};

xudClient.discoverNodes(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "numNodes": <uint32>
// }
```
```python
request = xud.DiscoverNodesRequest(
  node_identifier=<string>,
)
response = xudStub.DiscoverNodes(request)
print(response)
# Output:
# {
#  "num_nodes": <uint32>
# }
```
Discover nodes from a specific peer and apply new connections
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer to discover nodes from.
### Response
Parameter | Type | Description
--------- | ---- | -----------
num_nodes | uint32 | 
## GetBalance
```javascript
var request = {
  currency: <string>,
};

xudClient.getBalance(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "balances": <Balance>
// }
```
```python
request = xud.GetBalanceRequest(
  currency=<string>,
)
response = xudStub.GetBalance(request)
print(response)
# Output:
# {
#  "balances": <Balance>
# }
```
```shell
  xucli getbalance [currency]
  ```
Gets the total balance available across all payment channels and wallets for one or all currencies.
### Request
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to query for, if unspecified then balances for all supported currencies are queried.
### Response
Parameter | Type | Description
--------- | ---- | -----------
balances | map&lt;string, [Balance](#balance)&gt; | A map between currency ticker symbols and their balances.
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
//  "numPeers": <uint32>,
//  "numPairs": <uint32>,
//  "orders": <OrdersCount>,
//  "lnd": <LndInfo>,
//  "alias": <string>,
//  "network": <string>,
//  "pendingSwapHashes": <string[]>,
//  "connext": <ConnextInfo>
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
#  "num_peers": <uint32>,
#  "num_pairs": <uint32>,
#  "orders": <OrdersCount>,
#  "lnd": <LndInfo>,
#  "alias": <string>,
#  "network": <string>,
#  "pending_swap_hashes": <string[]>,
#  "connext": <ConnextInfo>
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
num_peers | uint32 | The number of currently connected peers.
num_pairs | uint32 | The number of supported trading pairs.
orders | [OrdersCount](#orderscount) | The number of active, standing orders in the order book.
lnd | map&lt;string, [LndInfo](#lndinfo)&gt; | 
alias | string | The alias of this instance of xud.
network | string | The network of this node.
pending_swap_hashes | string array | 
connext | [ConnextInfo](#connextinfo) | 
## GetMnemonic
```javascript
var request = {};

xudClient.getMnemonic(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "seedMnemonic": <string[]>
// }
```
```python
request = xud.GetMnemonicRequest()
response = xudStub.GetMnemonic(request)
print(response)
# Output:
# {
#  "seed_mnemonic": <string[]>
# }
```
```shell
  xucli getnemonic
  ```
Gets the master seed mnemonic .
### Request
This request has no parameters.
### Response
Parameter | Type | Description
--------- | ---- | -----------
seed_mnemonic | string array | 
## GetNodeInfo
```javascript
var request = {
  nodeIdentifier: <string>,
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
//  "reputationScore": <sint32>,
//  "banned": <bool>
// }
```
```python
request = xud.GetNodeInfoRequest(
  node_identifier=<string>,
)
response = xudStub.GetNodeInfo(request)
print(response)
# Output:
# {
#  "reputationScore": <sint32>,
#  "banned": <bool>
# }
```
```shell
  xucli getnodeinfo <node_identifier>
  ```
Gets general information about a node.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the node for which to get information.
### Response
Parameter | Type | Description
--------- | ---- | -----------
reputationScore | sint32 | The node's reputation score. Points are subtracted for unexpected or potentially malicious behavior. Points are added when swaps are successfully executed.
banned | bool | Whether the node is currently banned.
## ListOrders
```javascript
var request = {
  pairId: <string>,
  owner: <Owner>,
  limit: <uint32>,
  includeAliases: <bool>,
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
  owner=<Owner>,
  limit=<uint32>,
  include_aliases=<bool>,
)
response = xudStub.ListOrders(request)
print(response)
# Output:
# {
#  "orders": <Orders>
# }
```
```shell
  xucli listorders [pair_id] [include_own_orders] [limit]
  ```
Gets orders from the order book. This call returns the state of the order book at a given point in time, although it is not guaranteed to still be vaild by the time a response is received and processed by a client. It accepts an optional trading pair id parameter. If specified, only orders for that particular trading pair are returned. Otherwise, all orders are returned. Orders are separated into buys and sells for each trading pair, but unsorted.
### Request
Parameter | Type | Description
--------- | ---- | -----------
pair_id | string | The trading pair for which to retrieve orders.
owner | [Owner](#owner) | Whether only own, only peer or both orders should be included in result.
limit | uint32 | The maximum number of orders to return from each side of the order book.
include_aliases | bool | Whether to include the node aliases of owners of the orders.
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
//  "currencies": <Currency[]>
// }
```
```python
request = xud.ListCurrenciesRequest()
response = xudStub.ListCurrencies(request)
print(response)
# Output:
# {
#  "currencies": <Currency[]>
# }
```
```shell
  xucli listcurrencies
  ```
Gets a list of this node's supported currencies.
### Request
This request has no parameters.
### Response
Parameter | Type | Description
--------- | ---- | -----------
currencies | [Currency](#currency) array | The list of available currencies in the orderbook.
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
## OpenChannel
```javascript
var request = {
  nodeIdentifier: <string>,
  currency: <string>,
  amount: <uint64>,
  pushAmount: <uint64>,
  fee: <uint64>,
};

xudClient.openChannel(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "transactionId": <string>
// }
```
```python
request = xud.OpenChannelRequest(
  node_identifier=<string>,
  currency=<string>,
  amount=<uint64>,
  push_amount=<uint64>,
  fee=<uint64>,
)
response = xudStub.OpenChannel(request)
print(response)
# Output:
# {
#  "transaction_id": <string>
# }
```
```shell
  xucli openchannel <currency> <amount> [node_identifier] [push_amount]
  ```
Opens a payment channel to a peer for the specified amount and currency.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer with which to open channel with.
currency | string | The ticker symbol of the currency to open the channel for.
amount | uint64 | The amount to be deposited into the channel denominated in satoshis.
push_amount | uint64 | The balance amount to be pushed to the remote side of the channel denominated in satoshis.
fee | uint64 | The manual fee rate set in sat/byte that should be used when crafting the funding transaction in the channel.
### Response
Parameter | Type | Description
--------- | ---- | -----------
transaction_id | string | The id of the transaction that opened the channel.
## PlaceOrder
```javascript
var request = {
  price: <double>,
  quantity: <uint64>,
  pairId: <string>,
  orderId: <string>,
  side: <OrderSide>,
  replaceOrderId: <string>,
  immediateOrCancel: <bool>,
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
//  "match": <Order>,
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
  replace_order_id=<string>,
  immediate_or_cancel=<bool>,
)
for response in stub.PlaceOrder(request):
  print(response)
# Output:
# {
#  "match": <Order>,
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
quantity | uint64 | The quantity of the order denominated in satoshis.
pair_id | string | The trading pair that the order is for.
order_id | string | The local id to assign to the order.
side | [OrderSide](#orderside) | Whether the order is a buy or sell.
replace_order_id | string | The local id of an existing order to be replaced. If provided, the order must be successfully found and removed before the new order is placed, otherwise an error is returned.
immediate_or_cancel | bool | Whether the order must be filled immediately and not allowed to enter the order book.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
match | [Order](#order) | An order (or portion thereof) that matched the newly placed order.
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
  replaceOrderId: <string>,
  immediateOrCancel: <bool>,
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
  replace_order_id=<string>,
  immediate_or_cancel=<bool>,
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
quantity | uint64 | The quantity of the order denominated in satoshis.
pair_id | string | The trading pair that the order is for.
order_id | string | The local id to assign to the order.
side | [OrderSide](#orderside) | Whether the order is a buy or sell.
replace_order_id | string | The local id of an existing order to be replaced. If provided, the order must be successfully found and removed before the new order is placed, otherwise an error is returned.
immediate_or_cancel | bool | Whether the order must be filled immediately and not allowed to enter the order book.
### Response
Parameter | Type | Description
--------- | ---- | -----------
internal_matches | [Order](#order) array | A list of own orders (or portions thereof) that matched the newly placed order.
swap_successes | [SwapSuccess](#swapsuccess) array | A list of successful swaps of peer orders that matched the newly placed order.
remaining_order | [Order](#order) | The remaining portion of the order, after matches, that enters the order book.
swap_failures | [SwapFailure](#swapfailure) array | A list of swap attempts that failed.
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
//  "amountReceived": <uint64>,
//  "amountSent": <uint64>,
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
#  "amount_received": <uint64>,
#  "amount_sent": <uint64>,
#  "peer_pub_key": <string>,
#  "role": <Role>,
#  "currency_received": <string>,
#  "currency_sent": <string>,
#  "r_preimage": <string>,
#  "price": <double>
# }
```
Executes a swap on a maker peer order.
### Request
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The order id of the maker order.
pair_id | string | The trading pair of the swap orders.
peer_pub_key | string | The node pub key of the peer which owns the maker order. This is optional but helps locate the order more quickly.
quantity | uint64 | The quantity to swap denominated in satoshis. The whole order will be swapped if unspecified.
### Response
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that was swapped.
local_id | string | The local id for the order that was swapped.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was swapped.
r_hash | string | The hex-encoded payment hash for the swap.
amount_received | uint64 | The amount received denominated in satoshis.
amount_sent | uint64 | The amount sent denominated in satoshis.
peer_pub_key | string | The node pub key of the peer that executed this order.
role | [Role](#role) | Our role in the swap, either MAKER or TAKER.
currency_received | string | The ticker symbol of the currency received.
currency_sent | string | The ticker symbol of the currency sent.
r_preimage | string | The hex-encoded preimage.
price | double | The price used for the swap.
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
//  "quantityOnHold": <uint64>,
//  "remainingQuantity": <uint64>,
//  "removedQuantity": <uint64>,
//  "pairId": <string>
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
#  "quantity_on_hold": <uint64>,
#  "remaining_quantity": <uint64>,
#  "removed_quantity": <uint64>,
#  "pair_id": <string>
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
quantity | uint64 | The quantity to remove from the order denominated in satoshis. If zero or unspecified then the entire order is removed.
### Response
Parameter | Type | Description
--------- | ---- | -----------
quantity_on_hold | uint64 | Any portion of the order that was on hold due to ongoing swaps at the time of the request and could not be removed until after the swaps finish.
remaining_quantity | uint64 | Remaining portion of the order if it was a partial removal.
removed_quantity | uint64 | Successfully removed portion of the order.
pair_id | string | Removed order's pairId. (e.g. ETH/BTC)
## RemoveAllOrders
```javascript
var request = {};

xudClient.removeAllOrders(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "removedOrderIds": <string[]>,
//  "onHoldOrderIds": <string[]>
// }
```
```python
request = xud.RemoveAllOrdersRequest()
response = xudStub.RemoveAllOrders(request)
print(response)
# Output:
# {
#  "removed_order_ids": <string[]>,
#  "on_hold_order_ids": <string[]>
# }
```
```shell
  xucli removeallorders
  ```
Removes all orders from the order book. Removed orders become immediately unavailable for swaps, and peers are notified that the orders are no longer valid. Any portion of the orders that is on hold due to ongoing swaps will not be removed until after the swap attempts complete.
### Request
This request has no parameters.
### Response
Parameter | Type | Description
--------- | ---- | -----------
removed_order_ids | string array | The local order ids that were successfully removed.
on_hold_order_ids | string array | The local order ids that were on hold and failed to be removed.
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
## SetLogLevel
```javascript
var request = {
  logLevel: <LogLevel>,
};

xudClient.setLogLevel(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output: {}
```
```python
request = xud.SetLogLevelRequest(
  log_level=<LogLevel>,
)
response = xudStub.SetLogLevel(request)
print(response)
# Output: {}
```
```shell
  xucli loglevel <level>
  ```
Set the logging level.
### Request
Parameter | Type | Description
--------- | ---- | -----------
log_level | [LogLevel](#loglevel) | 
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
## SubscribeOrders
```javascript
var request = {
  existing: <bool>,
};

var call = xudClient.subscribeOrders(request);
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
//  "order": <Order>,
//  "orderRemoval": <OrderRemoval>
// }
```
```python
request = xud.SubscribeOrdersRequest(
  existing=<bool>,
)
for response in stub.SubscribeOrders(request):
  print(response)
# Output:
# {
#  "order": <Order>,
#  "order_removal": <OrderRemoval>
# }
```
Subscribes to orders being added to and removed from the order book. This call allows the client to maintain an up-to-date view of the order book. For example, an exchange that wants to show its users a real time view of the orders available to them would subscribe to this streaming call to be alerted as new orders are added and expired orders are removed.
### Request
Parameter | Type | Description
--------- | ---- | -----------
existing | bool | Whether to transmit all existing active orders upon establishing the stream.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
order | [Order](#order) | An order that was added to the order book.
order_removal | [OrderRemoval](#orderremoval) | An order (or portion thereof) that was removed from the order book.
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
//  "amountReceived": <uint64>,
//  "amountSent": <uint64>,
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
#  "amount_received": <uint64>,
#  "amount_sent": <uint64>,
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
r_hash | string | The hex-encoded payment hash for the swap.
amount_received | uint64 | The amount received denominated in satoshis.
amount_sent | uint64 | The amount sent denominated in satoshis.
peer_pub_key | string | The node pub key of the peer that executed this order.
role | [Role](#role) | Our role in the swap, either MAKER or TAKER.
currency_received | string | The ticker symbol of the currency received.
currency_sent | string | The ticker symbol of the currency sent.
r_preimage | string | The hex-encoded preimage.
price | double | The price used for the swap.
## SubscribeSwapsAccepted
```javascript
var request = {};

var call = xudClient.subscribeSwapsAccepted(request);
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
//  "price": <double>,
//  "peerPubKey": <string>,
//  "rHash": <string>,
//  "amountReceiving": <uint64>,
//  "amountSending": <uint64>,
//  "currencyReceiving": <string>,
//  "currencySending": <string>
// }
```
```python
request = xud.SubscribeSwapsAcceptedRequest()
for response in stub.SubscribeSwapsAccepted(request):
  print(response)
# Output:
# {
#  "order_id": <string>,
#  "local_id": <string>,
#  "pair_id": <string>,
#  "quantity": <uint64>,
#  "price": <double>,
#  "peer_pub_key": <string>,
#  "r_hash": <string>,
#  "amount_receiving": <uint64>,
#  "amount_sending": <uint64>,
#  "currency_receiving": <string>,
#  "currency_sending": <string>
# }
```
Subscribes to accepted swaps. This stream emits a message when the local xud node accepts a swap request from a peer, but before the swap has actually succeeded.
### Request
This request has no parameters.
### Response (Streaming)
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that was accepted to be swapped.
local_id | string | The local id for the order that was accepted to be swapped.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was accepted to be swapped.
price | double | The price for the swap.
peer_pub_key | string | The node pub key of the peer that executed this order.
r_hash | string | The hex-encoded payment hash for the swap.
amount_receiving | uint64 | The amount received denominated in satoshis.
amount_sending | uint64 | The amount sent denominated in satoshis.
currency_receiving | string | The ticker symbol of the currency received.
currency_sending | string | The ticker symbol of the currency sent.
## TradeHistory
```javascript
var request = {
  limit: <uint32>,
};

xudClient.tradeHistory(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "trades": <Trade[]>
// }
```
```python
request = xud.TradeHistoryRequest(
  limit=<uint32>,
)
response = xudStub.TradeHistory(request)
print(response)
# Output:
# {
#  "trades": <Trade[]>
# }
```
```shell
  xucli tradehistory [limit]
  ```
Gets a list of completed trades.
### Request
Parameter | Type | Description
--------- | ---- | -----------
limit | uint32 | The maximum number of trades to return
### Response
Parameter | Type | Description
--------- | ---- | -----------
trades | [Trade](#trade) array | 
## TradingLimits
```javascript
var request = {
  currency: <string>,
};

xudClient.tradingLimits(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "limits": <TradingLimits>
// }
```
```python
request = xud.TradingLimitsRequest(
  currency=<string>,
)
response = xudStub.TradingLimits(request)
print(response)
# Output:
# {
#  "limits": <TradingLimits>
# }
```
```shell
  xucli tradinglimits [currency]
  ```
Gets the trading limits for one or all currencies.
### Request
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to query for, if unspecified then trading limits for all supported currencies are queried.
### Response
Parameter | Type | Description
--------- | ---- | -----------
limits | map&lt;string, [TradingLimits](#tradinglimits)&gt; | A map between currency ticker symbols and their trading limits.
## Unban
```javascript
var request = {
  nodeIdentifier: <string>,
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
  node_identifier=<string>,
  reconnect=<bool>,
)
response = xudStub.Unban(request)
print(response)
# Output: {}
```
```shell
  xucli unban <node_identifier> [reconnect]
  ```
Removes a ban from a node manually and, optionally, attempts to connect to it.
### Request
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer to unban.
reconnect | bool | Whether to attempt to connect to the peer after it is unbanned.
### Response
This response has no parameters.
## WalletWithdraw
```javascript
var request = {
  currency: <string>,
  destination: <string>,
  amount: <uint64>,
  all: <bool>,
  fee: <uint32>,
};

xudClient.walletWithdraw(request, function(err, response) {
  if (err) {
    console.error(err);
  } else {
    console.log(response);
  }
});
// Output:
// {
//  "transactionId": <string>
// }
```
```python
request = xud.WithdrawRequest(
  currency=<string>,
  destination=<string>,
  amount=<uint64>,
  all=<bool>,
  fee=<uint32>,
)
response = xudStub.WalletWithdraw(request)
print(response)
# Output:
# {
#  "transaction_id": <string>
# }
```
```shell
  xucli withdraw [amount] [currency] <destination> [fee]
  ```
Withdraws a given currency from the xud wallets to a specified address.
### Request
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to withdraw.
destination | string | The address to withdraw funds to.
amount | uint64 | The amount to withdraw denominated in satoshis
all | bool | Whether to withdraw all available funds for this currency. If true, the amount field is ignored.
fee | uint32 | The fee to use for the withdrawal transaction denominated in satoshis per byte.
### Response
Parameter | Type | Description
--------- | ---- | -----------
transaction_id | string | The id of the withdrawal transaction.
# Messages
## AddCurrencyResponse
This message has no parameters.
## AddPairRequest
Parameter | Type | Description
--------- | ---- | -----------
base_currency | string | The base currency that is bought and sold for this trading pair.
quote_currency | string | The currency used to quote a price for the base currency.
## AddPairResponse
This message has no parameters.
## Balance
Parameter | Type | Description
--------- | ---- | -----------
total_balance | uint64 | Total balance denominated in satoshis.
channel_balance | uint64 | Sum of confirmed channel balances denominated in satoshis.
pending_channel_balance | uint64 | Sum of pending channel balances denominated in satoshis.
inactive_channel_balance | uint64 | Sum of inactive channel balances denominated in satoshis.
wallet_balance | uint64 | Confirmed wallet balance in satoshis.
unconfirmed_wallet_balance | uint64 | Unconfirmed wallet balance in satoshis.
## BanRequest
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the node to ban.
## BanResponse
This message has no parameters.
## Chain
Parameter | Type | Description
--------- | ---- | -----------
chain | string | The blockchain the swap client is on (eg bitcoin, litecoin)
network | string | The network the swap client is on (eg regtest, testnet, mainnet)
## Channels
Parameter | Type | Description
--------- | ---- | -----------
active | uint32 | The number of active/online channels for this lnd instance that can be used for swaps.
inactive | uint32 | The number of inactive/offline channels for this lnd instance.
pending | uint32 | The number of channels that are pending on-chain confirmation before they can be used.
closed | uint32 | The number of channels that have been closed.
## ChangePasswordRequest
Parameter | Type | Description
--------- | ---- | -----------
new_password | string | 
old_password | string | 
## ChangePasswordResponse
This message has no parameters.
## CloseChannelRequest
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer with which to close any channels with.
currency | string | The ticker symbol of the currency of the channel to close.
force | bool | Whether to force close the channel in case the peer is offline or unresponsive.
destination | string | The on-chain address to send funds extracted from the channel. If unspecified, the funds return to the default wallet for the client closing the channel.
amount | uint64 | For Connext only - the amount to extract from the channel. If 0 or unspecified, the entire off-chain balance for the specified currency will be extracted.
fee | uint64 | A manual fee rate set in sat/byte that should be used when crafting the closure transaction.
## CloseChannelResponse
Parameter | Type | Description
--------- | ---- | -----------
transaction_ids | string array | The id of the transaction per channel close.
## ConnectRequest
Parameter | Type | Description
--------- | ---- | -----------
node_uri | string | The uri of the node to connect to in "[nodePubKey]@[host]:[port]" format.
## ConnectResponse
This message has no parameters.
## CreateNodeRequest
Parameter | Type | Description
--------- | ---- | -----------
password | string | The password in utf-8 with which to encrypt the new xud node key as well as any uninitialized underlying wallets.
## CreateNodeResponse
Parameter | Type | Description
--------- | ---- | -----------
seed_mnemonic | string array | The 24 word mnemonic to recover the xud identity key and underlying wallets
initialized_lnds | string array | The list of lnd clients that were initialized.
initialized_connext | bool | Whether the connext wallet was initialized.
## Currency
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol for this currency such as BTC, LTC, ETH, etc...
swap_client | [SwapClient](#swapclient) | The payment channel network client to use for executing swaps.
token_address | string | The contract address for layered tokens such as ERC20.
decimal_places | uint32 | The number of places to the right of the decimal point of the smallest subunit of the currency. For example, BTC, LTC, and others where the smallest subunits (satoshis) are 0.00000001 full units (bitcoins) have 8 decimal places. ETH has 18. This can be thought of as the base 10 exponent of the smallest subunit expressed as a positive integer. A default value of 8 is used if unspecified.
## DepositRequest
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to deposit.
## DepositResponse
Parameter | Type | Description
--------- | ---- | -----------
address | string | The address to use to deposit funds.
## DiscoverNodesRequest
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer to discover nodes from.
## DiscoverNodesResponse
Parameter | Type | Description
--------- | ---- | -----------
num_nodes | uint32 | 
## ExecuteSwapRequest
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The order id of the maker order.
pair_id | string | The trading pair of the swap orders.
peer_pub_key | string | The node pub key of the peer which owns the maker order. This is optional but helps locate the order more quickly.
quantity | uint64 | The quantity to swap denominated in satoshis. The whole order will be swapped if unspecified.
## GetBalanceRequest
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to query for, if unspecified then balances for all supported currencies are queried.
## GetBalanceResponse
Parameter | Type | Description
--------- | ---- | -----------
balances | map&lt;string, [Balance](#balance)&gt; | A map between currency ticker symbols and their balances.
## GetInfoRequest
This message has no parameters.
## GetInfoResponse
Parameter | Type | Description
--------- | ---- | -----------
version | string | The version of this instance of xud.
node_pub_key | string | The node pub key of this node.
uris | string array | A list of uris that can be used to connect to this node. These are shared with peers.
num_peers | uint32 | The number of currently connected peers.
num_pairs | uint32 | The number of supported trading pairs.
orders | [OrdersCount](#orderscount) | The number of active, standing orders in the order book.
lnd | map&lt;string, [LndInfo](#lndinfo)&gt; | 
alias | string | The alias of this instance of xud.
network | string | The network of this node.
pending_swap_hashes | string array | 
connext | [ConnextInfo](#connextinfo) | 
## GetMnemonicRequest
This message has no parameters.
## GetMnemonicResponse
Parameter | Type | Description
--------- | ---- | -----------
seed_mnemonic | string array | 
## GetNodeInfoRequest
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the node for which to get information.
## GetNodeInfoResponse
Parameter | Type | Description
--------- | ---- | -----------
reputationScore | sint32 | The node's reputation score. Points are subtracted for unexpected or potentially malicious behavior. Points are added when swaps are successfully executed.
banned | bool | Whether the node is currently banned.
## ListCurrenciesRequest
This message has no parameters.
## ListCurrenciesResponse
Parameter | Type | Description
--------- | ---- | -----------
currencies | [Currency](#currency) array | The list of available currencies in the orderbook.
## ListOrdersRequest
Parameter | Type | Description
--------- | ---- | -----------
pair_id | string | The trading pair for which to retrieve orders.
owner | [Owner](#owner) | Whether only own, only peer or both orders should be included in result.
limit | uint32 | The maximum number of orders to return from each side of the order book.
include_aliases | bool | Whether to include the node aliases of owners of the orders.
## ListOrdersResponse
Parameter | Type | Description
--------- | ---- | -----------
orders | map&lt;string, [Orders](#orders)&gt; | A map between pair ids and their buy and sell orders.
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
## LndInfo
Parameter | Type | Description
--------- | ---- | -----------
status | string | 
channels | [Channels](#channels) | 
chains | [Chain](#chain) array | 
blockheight | uint32 | 
uris | string array | 
version | string | 
alias | string | 
## NodeIdentifier
Parameter | Type | Description
--------- | ---- | -----------
node_pub_key | string | The pub key of this node
alias | string | An alias for this node deterministically generated from the pub key
## OpenChannelRequest
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer with which to open channel with.
currency | string | The ticker symbol of the currency to open the channel for.
amount | uint64 | The amount to be deposited into the channel denominated in satoshis.
push_amount | uint64 | The balance amount to be pushed to the remote side of the channel denominated in satoshis.
fee | uint64 | The manual fee rate set in sat/byte that should be used when crafting the funding transaction in the channel.
## OpenChannelResponse
Parameter | Type | Description
--------- | ---- | -----------
transaction_id | string | The id of the transaction that opened the channel.
## Order
Parameter | Type | Description
--------- | ---- | -----------
price | double | The price of the order.
quantity | uint64 | The quantity of the order in satoshis.
pair_id | string | The trading pair that this order is for.
id | string | A UUID for this order.
node_identifier | [NodeIdentifier](#nodeidentifier) | The identifier of the node that created this order.
local_id | string | The local id for this order, if applicable.
created_at | uint64 | The epoch time in milliseconds when this order was created.
side | [OrderSide](#orderside) | Whether this order is a buy or sell
is_own_order | bool | Whether this order is a local own order or a remote peer order.
hold | uint64 | The quantity on hold pending swap execution.
## OrderRemoval
Parameter | Type | Description
--------- | ---- | -----------
quantity | uint64 | The quantity removed from the order.
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
peer | uint32 | The number of orders belonging to remote xud nodes.
own | uint32 | The number of orders belonging to our local xud node.
## OrderUpdate
Parameter | Type | Description
--------- | ---- | -----------
order | [Order](#order) | An order that was added to the order book.
order_removal | [OrderRemoval](#orderremoval) | An order (or portion thereof) that was removed from the order book.
## Peer
Parameter | Type | Description
--------- | ---- | -----------
address | string | The socket address with host and port for this peer.
node_pub_key | string | The node pub key to uniquely identify this peer.
lnd_pub_keys | map&lt;string, string&gt; | A map of ticker symbols to lnd pub keys for this peer
inbound | bool | Indicates whether this peer was connected inbound.
pairs | string array | A list of trading pair tickers supported by this peer.
xud_version | string | The version of xud being used by the peer.
seconds_connected | uint32 | The time in seconds that we have been connected to this peer.
alias | string | The alias for this peer's public key
## PlaceOrderRequest
Parameter | Type | Description
--------- | ---- | -----------
price | double | The price of the order.
quantity | uint64 | The quantity of the order denominated in satoshis.
pair_id | string | The trading pair that the order is for.
order_id | string | The local id to assign to the order.
side | [OrderSide](#orderside) | Whether the order is a buy or sell.
replace_order_id | string | The local id of an existing order to be replaced. If provided, the order must be successfully found and removed before the new order is placed, otherwise an error is returned.
immediate_or_cancel | bool | Whether the order must be filled immediately and not allowed to enter the order book.
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
match | [Order](#order) | An order (or portion thereof) that matched the newly placed order.
swap_success | [SwapSuccess](#swapsuccess) | A successful swap of a peer order that matched the newly placed order.
remaining_order | [Order](#order) | The remaining portion of the order, after matches, that enters the order book.
swap_failure | [SwapFailure](#swapfailure) | A swap attempt that failed.
## ConnextInfo
Parameter | Type | Description
--------- | ---- | -----------
status | string | 
address | string | 
version | string | 
chain | string | 
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
quantity | uint64 | The quantity to remove from the order denominated in satoshis. If zero or unspecified then the entire order is removed.
## RemoveOrderResponse
Parameter | Type | Description
--------- | ---- | -----------
quantity_on_hold | uint64 | Any portion of the order that was on hold due to ongoing swaps at the time of the request and could not be removed until after the swaps finish.
remaining_quantity | uint64 | Remaining portion of the order if it was a partial removal.
removed_quantity | uint64 | Successfully removed portion of the order.
pair_id | string | Removed order's pairId. (e.g. ETH/BTC)
## RemoveAllOrdersRequest
This message has no parameters.
## RemoveAllOrdersResponse
Parameter | Type | Description
--------- | ---- | -----------
removed_order_ids | string array | The local order ids that were successfully removed.
on_hold_order_ids | string array | The local order ids that were on hold and failed to be removed.
## RemovePairRequest
Parameter | Type | Description
--------- | ---- | -----------
pair_id | string | The trading pair ticker to remove in a format such as "LTC/BTC".
## RemovePairResponse
This message has no parameters.
## RestoreNodeRequest
Parameter | Type | Description
--------- | ---- | -----------
seed_mnemonic | string array | The 24 word mnemonic to recover the xud identity key and underlying wallets
password | string | The password in utf-8 with which to encrypt the restored xud node key as well as any restored underlying wallets.
lnd_backups | map&lt;string, bytes&gt; | A map between the currency of the LND and its multi channel SCB
xud_database | bytes | The XUD database backup
## RestoreNodeResponse
Parameter | Type | Description
--------- | ---- | -----------
restored_lnds | string array | The list of lnd clients that were initialized.
restored_connext | bool | Whether the connext wallet was initialized.
## SetLogLevelRequest
Parameter | Type | Description
--------- | ---- | -----------
log_level | [LogLevel](#loglevel) | 
## SetLogLevelResponse
This message has no parameters.
## ShutdownRequest
This message has no parameters.
## ShutdownResponse
This message has no parameters.
## SubscribeOrdersRequest
Parameter | Type | Description
--------- | ---- | -----------
existing | bool | Whether to transmit all existing active orders upon establishing the stream.
## SubscribeSwapsAcceptedRequest
This message has no parameters.
## SubscribeSwapsRequest
Parameter | Type | Description
--------- | ---- | -----------
include_taker | bool | Whether to include the results for swaps initiated via the PlaceOrder or ExecuteSwap calls. These swap results are also returned in the responses for the respective calls.
## SwapAccepted
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that was accepted to be swapped.
local_id | string | The local id for the order that was accepted to be swapped.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was accepted to be swapped.
price | double | The price for the swap.
peer_pub_key | string | The node pub key of the peer that executed this order.
r_hash | string | The hex-encoded payment hash for the swap.
amount_receiving | uint64 | The amount received denominated in satoshis.
amount_sending | uint64 | The amount sent denominated in satoshis.
currency_receiving | string | The ticker symbol of the currency received.
currency_sending | string | The ticker symbol of the currency sent.
## SwapFailure
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that failed the swap.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was attempted to be swapped.
peer_pub_key | string | The node pub key of the peer that we attempted to swap with.
failure_reason | string | The reason why the swap failed.
## SwapSuccess
Parameter | Type | Description
--------- | ---- | -----------
order_id | string | The global UUID for the order that was swapped.
local_id | string | The local id for the order that was swapped.
pair_id | string | The trading pair that the swap is for.
quantity | uint64 | The order quantity that was swapped.
r_hash | string | The hex-encoded payment hash for the swap.
amount_received | uint64 | The amount received denominated in satoshis.
amount_sent | uint64 | The amount sent denominated in satoshis.
peer_pub_key | string | The node pub key of the peer that executed this order.
role | [Role](#role) | Our role in the swap, either MAKER or TAKER.
currency_received | string | The ticker symbol of the currency received.
currency_sent | string | The ticker symbol of the currency sent.
r_preimage | string | The hex-encoded preimage.
price | double | The price used for the swap.
## Trade
Parameter | Type | Description
--------- | ---- | -----------
maker_order | [Order](#order) | The maker order involved in this trade.
taker_order | [Order](#order) | The taker order involved in this trade. Note that when a trade occurs from a remote peer filling one of our orders, we do not receive the order (only a swap request) and this field will be empty.
r_hash | string | The payment hash involved in this trade.
quantity | uint64 | The quantity transacted in this trade.
pair_id | string | The trading pair for this trade.
price | double | The price used for the trade.
role | [Role](#role) | Our role in the trade.
executed_at | uint64 | The epoch time in milliseconds that this trade was executed
side | [OrderSide](#orderside) | Whether this node was on the buy or sell side of the trade - or both in case of internal trades.
counterparty | [NodeIdentifier](#nodeidentifier) | The counterparty to this trade, if applicable.
## TradeHistoryRequest
Parameter | Type | Description
--------- | ---- | -----------
limit | uint32 | The maximum number of trades to return
## TradeHistoryResponse
Parameter | Type | Description
--------- | ---- | -----------
trades | [Trade](#trade) array | 
## TradingLimits
Parameter | Type | Description
--------- | ---- | -----------
max_sell | uint64 | Maximum outbound limit for a sell order denominated in satoshis.
max_buy | uint64 | Maximum inbound limit for a buy order denominated in satoshis.
reserved_sell | uint64 | The outbound amount reserved for open sell orders.
reserved_buy | uint64 | The inbound amount reserved for open buy orders.
## TradingLimitsRequest
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to query for, if unspecified then trading limits for all supported currencies are queried.
## TradingLimitsResponse
Parameter | Type | Description
--------- | ---- | -----------
limits | map&lt;string, [TradingLimits](#tradinglimits)&gt; | A map between currency ticker symbols and their trading limits.
## UnbanRequest
Parameter | Type | Description
--------- | ---- | -----------
node_identifier | string | The node pub key or alias of the peer to unban.
reconnect | bool | Whether to attempt to connect to the peer after it is unbanned.
## UnbanResponse
This message has no parameters.
## UnlockNodeRequest
Parameter | Type | Description
--------- | ---- | -----------
password | string | The password in utf-8 with which to unlock an existing xud node key as well as underlying client wallets such as lnd.
## UnlockNodeResponse
Parameter | Type | Description
--------- | ---- | -----------
unlocked_lnds | string array | The list of lnd clients that were unlocked.
locked_lnds | string array | The list of lnd clients that could not be unlocked.
## WithdrawRequest
Parameter | Type | Description
--------- | ---- | -----------
currency | string | The ticker symbol of the currency to withdraw.
destination | string | The address to withdraw funds to.
amount | uint64 | The amount to withdraw denominated in satoshis
all | bool | Whether to withdraw all available funds for this currency. If true, the amount field is ignored.
fee | uint32 | The fee to use for the withdrawal transaction denominated in satoshis per byte.
## WithdrawResponse
Parameter | Type | Description
--------- | ---- | -----------
transaction_id | string | The id of the withdrawal transaction.
# Enums
## OrderSide
Enumeration | Value | Description
----------- | ----- | -----------
BUY | 0 |
SELL | 1 |
BOTH | 2 |
## Role
Enumeration | Value | Description
----------- | ----- | -----------
TAKER | 0 |
MAKER | 1 |
INTERNAL | 2 |
## LogLevel
Enumeration | Value | Description
----------- | ----- | -----------
ALERT | 0 |
ERROR | 1 |
WARN | 2 |
INFO | 3 |
VERBOSE | 4 |
DEBUG | 5 |
TRACE | 6 |
## SwapClient
Enumeration | Value | Description
----------- | ----- | -----------
LND | 0 |
CONNEXT | 1 |
## Owner
Enumeration | Value | Description
----------- | ----- | -----------
BOTH | 0 |
OWN | 1 |
PEER | 2 |


# Python Code for Kite Zerodha Platform

* __Author: [TradeViaPython](https://www.youtube.com/c/TradeViaPython)__

## Installation

How to use:

```
# Download the 'kite_trade.py' file
# keep the file in same directory where your code file is stored
```

### Prerequisites

Python >=3.7

### Python Code Example
Import
```python

from kite_trade import *

```
Log In Method
```python
# # First Way to Login
# # You can use your Kite app in mobile
# # But You can't login anywhere in 'kite.zerodha.com' website else this session will disconnected

user_id = ""       # Login Id
password = ""      # Login password
twofa = ""         # Login Pin or TOTP

enctoken = get_enctoken(user_id, password, twofa)
kite = KiteApp(enctoken=enctoken)
```
Log In Method
```python
# # Second way is provide 'enctoken' manually from 'kite.zerodha.com' website
# # Than you can use login window of 'kite.zerodha.com' website Just don't logout from that window
# # # Process shared on YouTube 'TradeViaPython'

enctoken = ""
kite = KiteApp(enctoken=enctoken)
```

Other Methods
```python
# Basic calls
print(kite.profile())
print(kite.margins())
print(kite.orders())
print(kite.positions())

# Get instrument or exchange
print(kite.instruments())
print(kite.instruments("NSE"))
print(kite.instruments("NFO"))


# Get Tick Data 'Use Websocket'
from kiteconnect import KiteTicker
user_id = kite.profile()["user_id"]
kws = KiteTicker(api_key="TradeViaPython", access_token=enctoken+"&user_id="+user_id)

def on_ticks(ws, ticks):
    print(ticks)

kws.on_ticks = on_ticks
kws.connect(threaded=True)
while not kws.is_connected():
    time.sleep(1)
print("WebSocket : Connected")
kws.subscribe([256265, 260105, 738561, 5633])
kws.set_mode(kws.MODE_QUOTE, [256265, 260105, 738561, 5633])
time.sleep(30)
kws.unsubscribe([256265, 260105, 738561, 5633])


# Get Historical Data
import datetime
instrument_token = 9604354
from_datetime = datetime.datetime.now() - datetime.timedelta(days=7)     # From last & days
to_datetime = datetime.datetime.now()
interval = "5minute"
print(kite.historical_data(instrument_token, from_datetime, to_datetime, interval, continuous=False, oi=False))


# Place Order
order = kite.place_order(variety=kite.VARIETY_REGULAR,
                         exchange=kite.EXCHANGE_NSE,
                         tradingsymbol="ACC",
                         transaction_type=kite.TRANSACTION_TYPE_BUY,
                         quantity=1,
                         product=kite.PRODUCT_MIS,
                         order_type=kite.ORDER_TYPE_MARKET,
                         price=None,
                         validity=None,
                         disclosed_quantity=None,
                         trigger_price=None,
                         squareoff=None,
                         stoploss=None,
                         trailing_stoploss=None,
                         tag="TradeViaPython")

print(order)

# Modify order
kite.modify_order(variety=kite.VARIETY_REGULAR,
                  order_id="order_id",
                  parent_order_id=None,
                  quantity=5,
                  price=200,
                  order_type=kite.ORDER_TYPE_LIMIT,
                  trigger_price=None,
                  validity=kite.VALIDITY_DAY,
                  disclosed_quantity=None)

# Cancel order
kite.cancel_order(variety=kite.VARIETY_REGULAR,
                  order_id="order_id",
                  parent_order_id=None)
                  
```

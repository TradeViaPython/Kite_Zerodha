
# Python SDK for Kite Zerodha Platform

* __Author: [TradeViaPython](https://www.youtube.com/c/TradeViaPython)__
* **Current Version: 1.0.1**

## Installation

This module is installed via:

```
# Go to "dist" folder and download "AliceBlue_V2-1.0.1-py2.py3-none-any.whl" file
# In terminal go to the downloaded file location and install using

pip install AliceBlue_V2-1.0.1-py2.py3-none-any.whl
```

### Prerequisites

Python >=3.7

### Python Code Example
```python

########################################################################
# Required Details

api_key = "___"         # Get from https://a3.aliceblueonline.com      After Login go to "Apps" section and create API
user_id = "___"         # Aliceblue login user id

########################################################################
# Login Process

from AliceBlue_V2 import Alice

alice = Alice(user_id=user_id, api_key=api_key)
print(alice.create_session())       # Must "log in" to Alice platform before create session
alice.download_master_contract(to_csv=True)         # Download initially once a day

########################################################################
# Basic API Calls
print(alice.get_profile())
print(alice.get_balance())
print(alice.get_orderbook())
print(alice.get_trade_book())
print(alice.get_positions(alice.POSITION_DAYWISE))
print(alice.get_positions(alice.POSITION_NETWISE))
print(alice.get_holdings())

#######################################################################
# Get Master Contract
print(alice.get_master_contract())

# Get specific exchange
print(alice.get_master_contract("NSE"))
print(alice.get_master_contract("NFO"))
print(alice.get_master_contract("MCX"))   # BSE, CDS, BFO also available


import datetime
# Get Instrument
print(alice.get_instrument_by_symbol("NSE", "NIFTY BANK"))                  # NSE Indices NIFTY BANK
print(alice.get_instrument_by_symbol("NSE", "ACC-EQ"))                      # NSE Equity symbol
print(alice.get_instrument_by_symbol("NFO", "NIFTY22AUGFUT"))               # NFO Future symbol
print(alice.get_instrument_by_token("NSE", "3499"))
print(alice.get_instrument_for_fno("NFO", "BANKNIFTY", is_fut=True, expiry_date=datetime.date(2022, 8, 25)))
print(alice.get_instrument_for_fno("NFO", "BANKNIFTY", is_fut=False, expiry_date=datetime.date(2022, 8, 18), strike=39000, is_CE=True))


#########################################################################
# Place Order
order = alice.place_order(transaction_type=alice.TRANSACTION_TYPE_SELL,
                          instrument=alice.get_instrument_by_symbol("NFO", "NIFTY22AUGFUT"),
                          quantity=100,
                          price_type=alice.PRICE_TYPE_LIMIT,
                          product_type=alice.PRODUCT_TYPE_NRML,
                          price=17750.0,
                          trigger_price=None,
                          stop_loss=None,
                          square_off=None,
                          trailing_sl=None,
                          validity=alice.VALIDITY_IOC,
                          complexty=alice.COMPLEXTY_REGULAR,
                          order_tag="TradeViaPython")
print(order)

###########################################################################
# Modify Order
order = alice.modify_order(nestOrderNumber="22081300001897",
                           transaction_type=alice.TRANSACTION_TYPE_BUY,
                           instrument=alice.get_instrument_by_symbol("NSE", "ACC-EQ"),
                           quantity=10,
                           price_type=alice.PRICE_TYPE_LIMIT,
                           product_type=alice.PRODUCT_TYPE_CNC,
                           price=2227.0,
                           trigger_price=None)

print(order)

#############################################################################
# Cancel Order
order = alice.cancel_order(nestOrderNumber="220813000001897",
                           instrument=alice.get_instrument_by_symbol("NFO", "NIFTY22AUGFUT"))

print(order)

#############################################################################
# Place Bracket Order
order = alice.place_order(transaction_type=alice.TRANSACTION_TYPE_BUY,
                          instrument=alice.get_instrument_by_symbol("NSE", "RELIANCE-EQ"),
                          quantity=5,
                          price_type=alice.PRICE_TYPE_LIMIT,
                          product_type=alice.PRODUCT_TYPE_MIS,
                          price=2633.0,
                          trigger_price=None,
                          stop_loss=2620.0,
                          square_off=None,
                          trailing_sl=None,
                          validity=alice.VALIDITY_DAY,
                          complexty=alice.COMPLEXTY_BO,      # COMPLEXTY_AMO  for AMO order
                          order_tag="TradeViaPython")
print(order)

################################################################################
# WEBSOCKET
import time

socket_opened = False


def event_handler_quote_update(message):
    print(message)


def open_callback():
    global socket_opened
    socket_opened = True


alice.invalidate_socket_session()
alice.create_socket_session()
alice.start_websocket(subscribe_callback=event_handler_quote_update,
                      socket_open_callback=open_callback,
                      run_in_background=True)
while not socket_opened:
    pass
print("Websocket : Connected")
alice.subscribe([alice.get_instrument_by_symbol("NSE", i) for i in ["ACC-EQ", "RELIANCE-EQ", "UPL-EQ", "LUPIN-EQ"]])
time.sleep(30)
alice.unsubscribe([alice.get_instrument_by_symbol("NSE", i) for i in ["ACC-EQ", "RELIANCE-EQ"]])
time.sleep(10)


####################################################################################
# Get Historical Data
import datetime

instrument = alice.get_instrument_by_symbol("NSE", "LUPIN-EQ")
from_datetime = datetime.datetime.now() - datetime.timedelta(days=7)     # From last & days
to_datetime = datetime.datetime.now()                                    # To now
interval = "1"       # ["1", "2", "3", "4", "5", "10", "15", "30", "60", "120", "180", "240", "D", "1W", "1M"]
indices = False      # For Getting index data
print(alice.get_historical(instrument, from_datetime, to_datetime, interval, indices))

```

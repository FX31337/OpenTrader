# OpenTrader
## Open Trader

# OpenTrader

OpenTrader

https://github.com/OpenTrading/OpenTrader/

This project gives a command-line interpreter to communicate with
an OpenTrading Metatrader-Python bridge enabled MetaTrader
(https://github.com/OpenTrading/OTMql4AMQP/).
You can subscribe to tick, bar, and timer events,
query, open and close orders, and execute Metatrader code remotely.
If you have pyrabbit installed, you can query the AMQP server.

Coming Real Soon Now(TM) is backtesting of trading recipes,
reading Metatrader history into pandas DataFrames, plotting
DataFrames using matplotlib, and live-trading on Metatrader from recipes.

It builds on OTMql4AMQP, and requires that to be installed in your
Metatrader Python as a pre-requisite, if you want to do live trading.
In your Python, you also must have installed Pika:
https://pypi.python.org/pypi/pika/, which must be accessible to your
OTMql4Py (https://github.com/OpenTrading/OTMql4Py/).
You will have to call PikaCmd2 with the `-P` option with the
path of your installed Metatrader (e.g. `c:\Program Files\Metatrader`),
or add your installed OTMql4Py Python directory to the `PYTHONPATH`
environment variable (e.g. `c:\Program Files\Metatrader\MQL4\Python`).

**This is a work in progress - a developers' pre-release version.**

The project wiki should be open for editing by anyone logged into GitHub:
**Please report any system it works or doesn't work on in the wiki:
include the Metatrader build number, the origin of the metatrader exe,
the Windows version, and the AMQP server version and version of the Pika.**
This code in known to run under Linux Wine (1.7.x), so this project
bridges Metatrader to RabbitMQ under Linux.

### Installation

```
python setup.py
```
to create the PikaCmd2 script. Run `PikaCmd2 help` (see below).

In your Metatrader Python, you also must have installed Pika:
https://pypi.python.org/pypi/pika/ as well as
OTMql4AMQP (https://github.com/OpenTrading/OTMql4AMQP/),
OTMql4Py (https://github.com/OpenTrading/OTMql4Py/), and
OTMql4Lib (https://github.com/OpenTrading/OTMql4Lib/).

### Project

Please file any bugs in the issue tracker:
https://github.com/OpenTrading/OpenTrader/issues

Use the Wiki to start topics for discussion:
https://github.com/OpenTrading/OpenTrader/wiki
It's better to use the wiki for knowledge capture, and then we can pull
the important pages back into the documentation in the share/doc directory.
You will need to be signed into github.com to see or edit in the wiki.


## PikaCmd2

Documented commands (type help <topic>):
========================================
_load           chart           history  ord     publish  save       sub      
_relative_load  cmdenvironment  l        order   py       set        subscribe
bac             ed              li       orders  r        shell    
back            edit            list     pause   rabbit   shortcuts
backtest        hi              load     pub     run      show     

Undocumented commands:
======================
EOF  eof  exit  help  q  quit

### PikaCmd2 subscribe

Subscribe to messages from RabbitMQ on a given topic:
  sub topics            - shows topics subscribed to.
  sub run TOPIC1 ...    - start a thread to listen for messages,
                          TOPIC is one or more Rabbit topic patterns.
  sub hide TOPIC        - stop seeing TOPIC messages (e.g. tick - not a pattern)
  sub show              - list the message topics that are being hidden
  sub show TOPIC        - start seeing TOPIC messages (e.g. tick - not a pattern)
  sub pprint ?0|1       - seeing TOPIC messages with pretty-printing,
                          with 0 - off, 1 - on, no argument - current value
  sub thread info       - info on the thread listening for messages.
  sub thread stop       - stop a thread listening for messages.
  
Common RabbitMQ topic patterns are: # for all messages, tick.# for ticks,
timer.# for timer events, retval.# for return values.
You can choose as specific chart with syntax like:
    tick.oChart.EURGBP.240.93ACD6A2.#

Usage: sub [options] command

Options:
  -h, --help            show this help message and exit
  -c SCHARTID, --chart=SCHARTID
                        the target chart to subscribe to

### PikaCmd2 publish

Publish a message via RabbitMQ to a given chart on a OTMql4Py enabled terminal:
  pub cmd  COMMAND ARG1 ... - publish a Mql command to Mt4,
      the command should be a single string, with a space seperating arguments.
  pub eval COMMAND ARG1 ... - publish a Python command to the OTMql4Py,
      the command should be a single string, with a space seperating arguments.

You wont see the return value unless you have already done a:
  sub run retval.#

Usage: pub [options] command

Options:
  -h, --help            show this help message and exit
  -c SCHARTID, --chart=SCHARTID
                        the target chart to publish to (or: ANY ALL NONE)

### PikaCmd2 chart

Set and query the chart used for messages to and from RabbitMQ:
  list   - all the charts the listener has heard of,
           iff you have already started a listener with "sub run"
  get    - get the default chart to be published or subscribed to.
  set ID - set the default chart ID to be published or subscribed to.
  set    - set the default chart to be the last chart the listener has heard of,
           iff you have already started a listener with "sub run"

The chart ID will look something like: oChart_EURGBP_240_93ACD6A2_1

Usage: chart [options] command

Options:
  -h, --help  show this help message and exit

### PikaCmd2 order

  ord list          - list the ticket numbers of current orders.
  ord info iTicket  - list the current order information about iTicket.
  ord trades        - list the details of current orders.
  ord history       - list the details of closed orders.
  ord close iTicket [fPrice iSlippage]            - close an order;
                    Without the fPrice and iSlippage it will be a market order.
  ord buy|sell sSymbol fVolume [fPrice iSlippage] - send an order to open;
                    Without the fPrice and iSlippage it will be a market order.
  ord stoploss
  ord trail
  ord exposure      - total exposure of all orders, worst case scenario
  

Usage: ord [options] command

Options:
  -h, --help            show this help message and exit
  -c SCHARTID, --chart=SCHARTID
                        the target chart to order with (or: ANY ALL NONE)

### PikaCmd2 backtest

back feed dir
back feed dir dirname

back feed read_mt4_csv SYMBOL TIMEFRAME [YEAR]
back feed read_yahoo_csv SYMBOL [STARTYEAR]
back feed info
back feed plot

back recipe list
back recipe set
back recipe show

back results signals
back results trades
back results positions
back results equity
back results summary
back results display

back plot show
back plot set
back plot trades
back plot equity

Usage: back [options] command

Options:
  -h, --help            show this help message and exit
  -b SBACKTESTER, --backtester=SBACKTESTER
                        the backtest package (one of: pybacktest)

### PikaCmd2 rabbit

If we have pyrabbit installed, and iff the rabbitmq_management plugin
has been installed in your server, we can introspect some useful
information if the HTTP interface is enabled. Commands include:
    get vhost_names|channels|connections|queues

Usage: rabbit [options] command

Options:
  -h, --help            show this help message and exit
  -a SHTTPADDRESS, --address=SHTTPADDRESS
                        the TCP address of the HTTP rabbitmq_management
                        (default 127.0.0.1)
  -p IHTTPPORT, --port=IHTTPPORT
                        the TCP port of the HTTP rabbitmq_management plugin
                        (default 15672)


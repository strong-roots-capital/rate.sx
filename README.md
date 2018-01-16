
*rate.sx* — console service for getting cryptocurrencies exchange rates

## Usage

You can access the service from a shell or from a Web browser:

To get current market capitalization (in USD) of the top ten cryptocoins in shell:

```
    curl rate.sx
```

Or if you want to get the output in some other currency,
specify it in the domain name (lower-, upper- or mixed-case):

```
    curl eur.rate.sx
```

Also, you can use rate.sx in (crypto)currency calculator mode:

```
    $ curl eur.rate.sx/1BTC+1BCH+1BTG       # to convert sum of the bitcoins into Euro
    $ curl rub.rate.sx/100ETH               # to convert 100 ETH into Russian ruble
    $ curl rate.sx/1BTC-10ETH               # to compare what is more: 1 BTC or 10 ETH
```

To use it in a web browser, just type rate.sx in the location bar.

![rate.sx screenshot](http://rate.sx/files/screenshot.png)

## Features

* simple curl/browser interface
* available everywhere, no installation needed

## Supported currencies and cryptocurrencies

You can find actual list of the supported currencies in `/:help`.
32 different currencies and 500 most popular crypto currencies are supported at the moment.

## Options

For the list of all supported options see `/:help`:

```
    $ curl rate.sx/:help
```

The most important options:

    n=NUMBER            number of cryptocurrencies to show (10 by default)

Options are specified after the ? sign in the URL.
They can be separated using the `&` sign (don't forget to escape or to quote it in the shell, because
it is a special shell symbol).

```
    $ curl btc.rate.sx/?n=30
```

## Output units

By default, rate.sx shows rates of cryptocurrencies to USD (or any other currency if it is
specified in the query as show above). It is possible to use a crypto currency as the unit.
Keep in mind, that in the changes columns (and in the sparklines) difference to the historical
rates is displayed, and not to the current rate. That is obviously the reason why spark 
for the rate currency to the same currency is always 1 (and change is 0).

This mode can be used to compare some cryptocurrency with the rest.
Say, if we want to see, what cryptocurrencies are falling not so fast comparing 
to BTC (picture is done during the January 2018 correction):

![btc.rate.sx/?n=30](http://rate.sx/files/ratesx-n30.png)

We see here that nothing (except ARDR) from the top 30 (because we use `?n=30` here)
is falling slower than BTC (USDT is obviously an exception).
If we would use normal USD/EUR/GBP output, we could not find this out (at least not instantly).

## Disclaimer

Though *rate.sx* synchronizes with online cryptocurrencies exchanges every five minutes,
we cannot guarantee absolute accuracy of the displayed exchange rates.
You should always confirm current rates before making any transactions
that could be affected by changes in the exchange rates.
Crypocurrency rates based on the data provided by exchanges APIs.
All rates are for information purposes only and are subject to change without prior notice.
Since rates for actual transactions may vary,
we are not offering to enter into any transaction at any rate displayed.
Displayed rates are composite prices and not intended to be used for investment purposes. 

## Integration

### GNU Emacs

[rate-sx.el](https://github.com/davep/rate-sx.el) — rate.sx in Emacs (courtesy of Dave Pearson @davep)

![rate-sx.el screenshot](https://user-images.githubusercontent.com/28237/33782065-1569d88e-dc4f-11e7-9547-c9e14dcfd470.png)

## rate.sx Server Installation

If you want to install the *rate.sx* server, you can do it. Keep in mind that you need some data 
datasource. A server without data is useless (of course, you can always
use rate.sx as the datasource, though the point of such a strange configuration is not clear).

### Install rate.sx server

```
git clone github.com/chubin/rate.sx
cd rate.sx
virtualenv ve
ve/bin/pip install -r requirements.txt
ve/bin/python bin/srv.py

```

### Configure HTTP-frontend service

Configure the web server, that will be used to access the service (if you want to use a web frontend; it's recommended):

```
server {
    listen [::]:80;
    server_name  rate.sx *.rate.sx;
    access_log  /var/log/nginx/rate.sx-access.log  main;
    error_log  /var/log/nginx/rate.sx-error.log;

    location / {
        proxy_pass         http://127.0.0.1:8003;

        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $remote_addr;

        client_max_body_size       10m;
        client_body_buffer_size    128k;

        proxy_connect_timeout      90;
        proxy_send_timeout         90;
        proxy_read_timeout         90;

        proxy_buffer_size          4k;
        proxy_buffers              4 32k;
        proxy_busy_buffers_size    64k;
        proxy_temp_file_write_size 64k;

        expires                    off;
    }
}
```



# Monitoring utilities for Ethereum nodes

```
apt install -y git libjson-xs-perl libjson-perl libwww-perl
git clone https://github.com/cc32d9/geth-utils.git /opt/geth-utils
```


## Nagios plugin

The plugin compares the latest block timestamp with computer clock and
issues a warning or critical status if it's too far in the past.

```
/opt/geth-utils/nagios/check_geth_sync --url=http://127.0.0.1:8545
```

## haproxy checker

```
global
        log /dev/log    local1 notice
        stats socket /run/haproxy/admin.sock mode 666 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon
        external-check

frontend bscfe
    bind 127.0.0.1:8545
    mode http
    use_backend bscbe
    option  dontlognull
    option log-separate-errors

backend bscbe
    mode http
    balance roundrobin
    option allbackups
    option external-check
    external-check command /opt/geth-utils/haproxy/h_check_geth_sync
    server bsc01 1.2.3.4:8545 check inter 15s
    server bsc02 4.3.2.1:8545 backup check inter 15s
```


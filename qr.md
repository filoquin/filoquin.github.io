# QR con git aggregator
```console
filoquin@local:~$ pip install git-aggregator
```
creo *repros.yaml* con este contenido

``` yaml
odoo-argentina-ce:
    defaults:
        depth: 20
    remotes:
        origin: https://github.com/ingadhoc/odoo-argentina-ce.git
    merges:
        - origin 13.0
        - origin refs/pull/17/head
    target: origin 13.0_qr
```

Ejecuto 

```console
filoquin@local:~$ gitaggregate -c repos.yaml
```

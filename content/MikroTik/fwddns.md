---
title: "DNS FWD на MikroTik"
tags:
  - dns
  - mikrotik
draft: false
---

## Интро
FWD это особый тип DNS-записей в MikroTik позволяет перенаправитиь DNS-запросы для выбранного домена на сервер отличный от основного через Forwarder.
Forwarder в свою очередь это сущность внутри маршрутизатора которая перенаправляет запросы к выбраному классическому DNS или DNS over HTTPS серверу. Бонусом ко всему этому является то что в настройках FWD записи можно указать AddressList куда попадет зарезолвленный форвардером ip. Благодаря этому мы можем внести наши зацензуренные как FWD-записи и они сложаться в список адресов при отправке на них запросов. И то что есть чекбокс, позволяющий включать в этот список все поддомены, достойно отдельной благодарности.

## Настройка

Добавляем DNS Forwarder
```
/ip dns static
forwarders add doh-servers=https://cloudflare-dns.com/dns-query name=cf.dns verify-doh-cert=no
add comment="DNS for FWD" forward-to=cf.dns name=router.dns type=FWD
```
![[assets/dns-forwarder.png]]

Добавляем статичную DNS-запись которая будет перенаправлять запросы на forwarder

![[assets/router.dns.png]]

И добавим для примера домен torproject.org в список для обхода блокировки 

![[assets/staticdnsforfwd.png]]
```
add address-list=Censored comment=TOR forward-to=router.dns match-subdomain=yes name=\
    torproject.org type=FWD
```

## Маршрутизация

```

/routing table
add disabled=no fib name=to-vpn
/ip firewall mangle
add action=mark-routing chain=prerouting dst-address-list=Censored \
    new-routing-mark=to-vpn
```
Создаем таблицу баршрутизации **to-vpn** и маркируем для нее пакеты до доменов из нашего списка **Censored**

И в конце нужно прописать маршрут для всех пакетов из нашей новой таблицы к нашему VPN-интерфейсу

![[assets/routetovpn.png]]


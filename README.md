# Домашнее задание №1: VLAN и маршрутизация между VLAN
- Дана следующая схема:  
  ![alt-dtp](https://github.com/vk1391/OTUS_NE_IVR/blob/main/схема2.jpg "Схема к домашнему заданию №1")
- Таблица адресов:

**Имя Устройства** | **IP** | **Префикс** | **VLAN** | **Интерфейс**
 --- | --- | --- | --- | ---
| R1 | 192.168.3.1 | 24 | 3 | e0/0.3 
| R1 | 192.168.4.1 | 24 | 4 | e0/0.4 
| S1 | 192.168.3.11 | 24 | 3 | Vlan3 
| S2 | 192.168.3.12 | 24 | 3 | Vlan3 
| PC-A | 192.168.3.3 | 24 | 3 | eth0
| PC-B | 192.168.4.3 | 24 | 4 | eth0

- конфигурация R1:
```
hostname R1
no ip domain-lookup 
enable secret class
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
exit 
service password-encryption 
banner motd $ Authorized Users Only! $
clock timezone UTC 3 
int e0/0.3
encapsulation dot1Q 3
ip address 192.168.3.1 255.255.255.0 
R1(config)#int e0/0.4
encapsulation dot1Q 4
ip address 192.168.4.1 255.255.255.0
do wr me
```
- конфигурация S1:
```
hostname S1
no ip domain-lookup 
enable secret class
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
exit 
service password-encryption 
banner motd $ Authorized Users Only! $
clock timezone UTC 3 
S1(config)#int e0/0
S1(config-if)#switchport trunk encapsulation dot1q 
S1(config-if)sw m tr
S1(config-if)#exit
S1(config)#int e0/1
S1(config-if)#switchport trunk encapsulation dot1q 
S1(config-if)sw m tr
S1(config-if)#exit
S1(config)#int e 0/2
S1(config-if)#sw m ac
S1(config-if)#sw ac vlan 3
S1(config-if)#exit
S1(config)#interface vlan 3
S1(config-if)#ip address 192.168.3.11 255.255.255.0
S1(config-if)#no shutdown 
S1(config)#interface vlan 4
S1(config-if)#no shutdown 
```
- конфигурация S2:
```
hostname S2
no ip domain-lookup 
enable secret class
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
exit 
service password-encryption 
banner motd $ Authorized Users Only! $
clock timezone UTC 3 
S2(config)#int e0/1
S2(config-if)#switchport trunk encapsulation dot1q 
S2(config-if)sw m tr
S2(config-if)#exit
S2(config)#int e 0/2
S2(config-if)#sw m ac
S2(config-if)#sw ac vlan 4
S2(config-if)#exit
S2(config)#interface vlan 3
S2(config-if)#ip address 192.168.3.12 255.255.255.0
S2(config-if)#no shutdown 
S2(config)#interface vlan 4
S2(config-if)#no shutdown
```
- результат:  
  пинг С PC-A:
```
VPCS> ping 192.168.4.3

84 bytes from 192.168.4.3 icmp_seq=1 ttl=63 time=3.535 ms
84 bytes from 192.168.4.3 icmp_seq=2 ttl=63 time=4.876 ms
84 bytes from 192.168.4.3 icmp_seq=3 ttl=63 time=3.962 ms
84 bytes from 192.168.4.3 icmp_seq=4 ttl=63 time=4.857 ms
84 bytes from 192.168.4.3 icmp_seq=5 ttl=63 time=4.381 ms

VPCS> ping 192.168.3.1

84 bytes from 192.168.3.1 icmp_seq=1 ttl=255 time=1.575 ms
84 bytes from 192.168.3.1 icmp_seq=2 ttl=255 time=2.216 ms
^C
VPCS> ping 192.168.3.12

84 bytes from 192.168.3.12 icmp_seq=1 ttl=255 time=1.727 ms
84 bytes from 192.168.3.12 icmp_seq=2 ttl=255 time=2.353 ms
84 bytes from 192.168.3.12 icmp_seq=3 ttl=255 time=2.789 ms
^C
VPCS> ping 192.168.3.11

84 bytes from 192.168.3.11 icmp_seq=1 ttl=255 time=0.597 ms
84 bytes from 192.168.3.11 icmp_seq=2 ttl=255 time=1.457 ms
84 bytes from 192.168.3.11 icmp_seq=3 ttl=255 time=1.031 ms
```
трасировка с PC-B на PC-A
```
VPCS> trace 192.168.3.3
trace to 192.168.3.3, 8 hops max, press Ctrl+C to stop
 1   192.168.4.1   3.652 ms  2.514 ms  2.604 ms
 2   *192.168.3.3   5.660 ms
```



University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2022/2023  
Group: K33212  
Author: Dolmatov Dmitrii Alexeevich  
Lab: Lab1  
Date of create: 16.12.2022  
Date of finished: --.12.2022  

# Лабораторная работ №1 "Установка ContainerLab и развертывание тестовой сети связи"  
## Описание  
В данной лабораторной работе вы познакомитесь с инструментом ContainerLab, развернете тестовую сеть связи, настроите оборудование на базе Linux и RouterOS
## Цель работы  
Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т.д.
### Ход работы  
Для начала настроим каждое сетевое устройство, а именно:
1. R01.TEST
2. SW01.L3.01.TEST
3. SW02.L3.02.TEST
4. SW02.L3.01.TEST
5. PC1
6. PC2  
 
### Ниже приведён код конфигурации каждого из вышеописанных устройств
#### R01.TEST. Подключение через sudo ssh admin@172.20.20.2
<pre><code>
> /interface vlan  
> add interface=ether2 name=vlan10 vlan-id=10  
> add interface=ether2 name=vlan20 vlan-id=20  
> /interface wireless security-profiles  
> set [ find default=yes ] supplicant-identity=MikroTik  
> ip pool  
> add name=pool10 ranges=10.10.10.10-10.10.10.228  
> add name=pool20 ranges=10.10.20.10-10.10.20.228  
> /ip dhcp-server  
> add address-pool=pool10 disabled=no interface=vlan10 name=server10  
> add address-pool=pool20 disabled=no interface=vlan20 name=server20  
> /ip address  
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28  
> add address=10.10.10.1/24 interface=vlan10 network=10.10.10.0  
> add address=10.10.20.1/24 interface=vlan20 network=10.10.20.0  
> /ip dhcp-client  
> add disabled=no interface=ether1  
> /ip dhcp-server network  
> add address=10.10.10.0/24 gateway=10.10.10.1  
> add address=10.10.20.0/24 gateway=10.10.20.1  
> /system identity  
> set name=R01.TEST  
</code></pre>  

#### SW01.L3.01.TEST Подключение через sudo ssh admin@172.20.20.3 
<pre><code>  
> /interface bridge
> add name=bridge10  
> add name=bridge20  
> /interface vlan  
> add interface=ether2 name=vlan10 vlan-id=10  
> add interface=ether3 name=vlan101 vlan-id=10  
> add interface=ether2 name=vlan20 vlan-id=20    
> add interface=ether4 name=vlan201 vlan-id=20  
> /interface wireless security-profiles  
> set [ find default=yes ] supplicant-identity=MikroTik  
> /interface bridge port  
> add bridge=bridge20 interface=vlan20     
> add bridge=bridge10 interface=vlan10    
> add bridge=bridge20 interface=vlan203    
> add bridge=bridge10 interface=vlan103  
> /ip address  
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28  
> /ip dhcp-client  
> add disabled=no interface=ether1  
> add disabled=no interface=bridge10  
> add disabled=no interface=bridge20  
> /system identity  
> set name=SW01.L3.01.TEST  
</code></pre>  

#### SW02.L3.01.TEST Подключение через sudo ssh admin@172.20.20.4
<pre><code>
> /interface bridge  
> add name=bridge10  
> /interface vlan  
> add interface=ether2 name=vlan10 vlan-id=10  
> /interface wireless security-profiles  
> set [ find default=yes ] supplicant-identity=MikroTik  
> /interface bridge port  
> add bridge=bridge10 interface=vlan10  
> add bridge=bridge10 interface=ether3  
> /ip address  
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28  
> /ip dhcp-client  
> add disabled=no interface=ether1  
> add disabled=no interface=bridge10  
> /system identity  
> set name=SW02.L3.01.TEST  
</code></pre>  

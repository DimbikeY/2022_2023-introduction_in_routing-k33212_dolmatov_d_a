University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2022/2023  
Group: K33212  
Author: Dolmatov Dmitrii Alexeevich  
Lab: Lab3  
Date of create: 24.12.2022  
Date of finished: --.12.2022  

# Лабораторная работ №3 "Эмуляция распределенной корпоративной сети связи, настройка OSPF и MPLS, организация первого EoMPLS" 
## Описание  
Ваша задача подключить Нью-Йоркский офис к общей IP/MPLS сети и организовать EoMPLS между "SGI Prism" и компьютером инженеров в Санк-Петербурге.
## Цель работы  
Изучить протоколы OSPF и MPLS, механизмы организации EoMPLS.
### Ход работы  
Для начала настроим каждое сетевое устройство, а именно:
1. SGI Prism - сервер
2. R01.NY
3. R01.LND
4. R01.HKI
5. R01.SPB
6. R01.MSK
7. R01.LBN
8. PC1
 
### Ниже приведён код конфигурации каждого из вышеописанных устройств
#### R01.NY Подключение через sudo ssh admin@172.10.10.2
<pre><code>  
/interface bridge  
> add name=Lo0
> add name=EoMPLS_B  
> /interface vpls 
> add cisco-style=yes cisco-style-id=666 name=EoMPLS_VPLS remote-peer=6.6.6.6 pw-type=row-ethernet
> /interface wireless security-profiles
> set [ find default=yes ] supplicant-identity=MikroTik
> /routing ospf instance
> set [ find default=yes ] router-id=1.1.1.1
> /interface bridge port
> add bridge=EoMPLS_B interface=ether2
> add bridge=EoMPLS_B interface=EoMPLS_VPLS
> /ip address
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
> add address=172.16.1.1/30 interface=ether3 network=172.16.1.0
> add address=172.16.2.1/30 interface=ether4 network=172.16.2.0
> add address=1.1.1.1/32 interface=Lo0 network=1.1.1.1
> /ip dhcp-client
> add disabled=no interface=ether1
> /mpls ldp
> set enabled=yes transport-address=1.1.1.1
> /mpls ldp interface
> add interface=ether3
> add interface=ether4
> /routing ospf network
> add area=backbone
> /system identity
> set name=R01.NY
</code></pre>  
 
#### SW01.LND Подключение через sudo ssh admin@172.10.10.3 
<pre><code>  
> /interface bridge
> add name=Lo0
> /interface wireless security-profiles
> set [ find default=yes ] supplicant-identity=MikroTik
> /routing ospf instance
> set [ find default=yes ] router-id=2.2.2.2
> /ip address
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
> add address=172.16.1.2/30 interface=ether2 network=172.16.1.0
> add address=172.16.3.1/30 interface=ether3 network=172.16.3.0
> add address=2.2.2.2/32 interface=Lo0 network=2.2.2.2
> /ip dhcp-client
> add disabled=no interface=ether1
> /mpls ldp
> set enabled=yes transport-address=2.2.2.2
> /mpls ldp interface
> add interface=ether2
> add interface=ether3
> /routing ospf network
> add area=backbone
> /system identity
> set name=R01.LND 
</code></pre>  

#### R01.LBN Подключение через sudo ssh admin@172.20.20.4
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

#### SW02.L3.02.TEST Подключение через sudo ssh admin@172.20.20.6
<pre><code>     
> /interface bridge
> add name=bridge20
> /interface vlan
> add interface=ether2 name=vlan20 vlan-id=20
> /interface wireless security-profiles
> set [ find default=yes ] supplicant-identity=MikroTik
> /interface bridge port
> add bridge=bridge20 interface=vlan20
> add bridge=bridge20 interface=ether3
> /ip address
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
> /ip dhcp-client
> add disabled=no interface=ether1
> add disabled=no interface=bridge20
> /system identity
> set name=SW02.L3.02.TEST  
</code></pre>  

Файл конфигурации .yaml вы можете посмотреть [здесь](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab1/lab_1.yaml)  
Схема, начерченная в draw.io:![lab_1_scheme](https://user-images.githubusercontent.com/57844480/208727105-0626475b-3eed-4071-92ba-ffe65521d81f.png)  
  

Скриншоты, на которых изображены ping до конечных устройств, а также таблицу выданных ip адресов dhcp серверами, представлены ниже: <img width="473" alt="ping" src="https://user-images.githubusercontent.com/57844480/208727197-fce7c3d3-cac5-4d48-bb0b-368734528257.png">  
Связь интерфейсов:  <img width="536" alt="S01" src="https://user-images.githubusercontent.com/57844480/208727566-ae93fad4-69c8-43dd-a7cd-401e1b056c2e.png">  

Выданные ip:  <img width="653" alt="R01" src="https://user-images.githubusercontent.com/57844480/208727659-6302d949-665d-4b23-b2c5-5c78cbe39eeb.png">  

### Особенность, которую хотелось бы отметить:  
#### IP адрес интерфейса управления ether1: 172.31.255.30/30 несмотря на подключения по SSH по другому адресу. Особенность обёртки, которую нужно знать



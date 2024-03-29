University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://itmo-ict-faculty.github.io/network-programming/education/labs2023_2024)  
Year: 2023/2024 
Group: K34212  
Author: Dolmatov Dmitrii Alexeevich  
Lab: Lab1  
Date of create: 25.10.2022  
Date of finished: --.12.2022  

# Лабораторная работ №1 "Установка ContainerLab и развертывание тестовой сети связи"  
## Описание  
В данной лабораторной работе вы познакомитесь с инструментом ContainerLab, развернете тестовую сеть связи, настроите оборудование на базе Linux и RouterOS
## Цель работы  
Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т.д.
### Ход работы  
Для начала, были созданы
Файл конфигурации .yaml вы можете посмотреть [здесь](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab1/lab_1.yaml)   
Схема, начерченная в draw.io:![lab_1_scheme](https://user-images.githubusercontent.com/57844480/208727105-0626475b-3eed-4071-92ba-ffe65521d81f.png)  
 
### Ниже приведён код конфигурации каждого из вышеописанных устройств  
#### Подсеть: 172.20.20.0/24  
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

  
Скриншоты, на которых изображены ping до конечных устройств, а также таблицу выданных ip адресов dhcp серверами, представлены ниже: <img width="473" alt="ping" src="https://user-images.githubusercontent.com/57844480/208727197-fce7c3d3-cac5-4d48-bb0b-368734528257.png">  
Связь интерфейсов:  <img width="536" alt="S01" src="https://user-images.githubusercontent.com/57844480/208727566-ae93fad4-69c8-43dd-a7cd-401e1b056c2e.png">  

Выданные ip:  <img width="653" alt="R01" src="https://user-images.githubusercontent.com/57844480/208727659-6302d949-665d-4b23-b2c5-5c78cbe39eeb.png">  

### Особенность, которую хотелось бы отметить:  
#### IP адрес интерфейса управления ether1: 172.31.255.30/30 несмотря на подключения по SSH по другому адресу. Особенность обёртки, которую нужно знать



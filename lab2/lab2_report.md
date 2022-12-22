University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2022/2023  
Group: K33212  
Author: Dolmatov Dmitrii Alexeevich  
Lab: Lab2  
Date of create: 21.12.2022  
Date of finished: --.12.2022  
  
# Лабораторная работ №2 "Эмуляция распределенной корпоративной сети связи, настройка статической маршрутизации между филиалами"  
## Описание  
В данной лабораторной работе вы первый раз познакомитесь с компанией "RogaIKopita Games" LLC которая занимается разработкой мобильных игр с офисами в Москве, Франкфурте и Берлине. Для обеспечения работы своих офисов "RogaIKopita Games" вам как сетевому инженеру необходимо установить 3 роутера, назначить на них IP адресацию и поднять статическую маршрутизацию. В результате работы сотрудник из Москвы должен иметь возможность обмениваться данными с сотрудником из Франкфурта или Берлина и наоборот  
## Цель работы  
Ознакомиться с принципами планирования IP адресов, настройке статической маршрутизации и сетевыми функциями устройств  
### Ход работы  
#### Схема изображена на рисунке
Файл конфигурации .yaml вы можете посмотреть [здесь](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab2/lab_2.yaml)  
Схема, начерченная в draw.io:![lab_2_scheme](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab2/lab_2_scheme.png)    
#### У нас имеется 6 устройств, таких как:  
* PC1  
* PC2  
* PC3  
* R01.MSK  
* R01.FRT  
* R01.BRL  
#### Напишем текст конфигурации каждого из них  
Подсеть: 172.20.20.0/24    
R.01.BRL Подключение через ssh admin@172.20.20.2    
<pre><code>  
> /ip pool  
> add name=pool1 ranges=192.168.30.5-192.168.30.254
> /ip dhcp-server
> add address-pool=pool3 disabled=no interface=ether4 name=dhcp1
> /ip address
> add address=172.15.255.30/30 interface=ether1 network=172.15.255.28
> add address=10.10.20.3/24 interface=ether2 network=10.10.20.0
> add address=10.10.30.4/24 interface=ether3 network=10.10.30.0
> add address=192.168.30.1/24 interface=ether4 network=192.168.30.0
> /ip dhcp-client
> add disabled=no interface=ether1
> /ip route
> add distance=1 dst-address=192.168.10.0/24 gateway=10.10.20.3
> add distance=1 dst-address=192.168.20.0/24 gateway=10.10.30.3
> /system identity
> set name=R01.BRL  
</pre></code>  


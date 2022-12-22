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

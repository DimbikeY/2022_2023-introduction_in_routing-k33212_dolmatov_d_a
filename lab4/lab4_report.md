University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2022/2023  
Group: K33212  
Author: Dolmatov Dmitrii Alexeevich  
Lab: Lab4    
Date of create: 29.12.2022  
Date of finished: --.12.2022  

# Лабораторная работ №4 "Эмуляция распределенной корпоративной сети связи, настройка iBGP, организация L3VPN, VPLS"  
## Описание  
После организации вашей AS коллеги из отдела DEVOPS попросили вас сделать L3VPN между 3 офисами для служебных нужд. (Рисунок 1) Данный L3VPN проработал пару недель и коллеги из отдела DEVOPS попросили вас сделать VPLS для служебных нужд.  
## Цель работы  
Изучить протоколы BGP, MPLS и правила организации L3VPN и VPLS.
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
### Код файла конфигурации находится [здесь](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab4/lab_4.yaml)  

### Ниже приведён код конфигурации каждого из вышеописанных устройств
#### R01.NY Подключение через sudo ssh admin@172.10.10.2
<pre><code>
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default redistribute-connected=yes router-id=2.2.2.2
/routing ospf instance
set [ find default=yes ] router-id=2.2.2.2
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.4.20.1/24 interface=ether3 network=172.4.20.0
add address=192.168.10.10/24 interface=ether2 network=192.168.10.0
add address=2.2.2.2 interface=Lo network=2.2.2.2
/ip dhcp-client
add disabled=no interface=ether1
/ip route vrf
add export-route-targets=64666:10 import-route-targets=64666:10 interfaces=ether2 route-distinguisher=64666:10 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether3
/routing bgp instance vrf
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=3.3.3.3 remote-as=64666 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.NY
</pre></code>
 
#### R01.LND Подключение через sudo ssh admin@172.10.10.3 
<pre><code>
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=3.3.3.3
/routing ospf instance
set [ find default=yes ] router-id=3.3.3.3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=3.3.3.3 interface=Lo network=3.3.3.3
add address=172.4.20.2/24 interface=ether2 network=172.4.20.0
add address=172.4.30.1/24 interface=ether3 network=172.4.30.0
add address=172.4.40.1/24 interface=ether4 network=172.4.40.0
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=2.2.2.2 remote-as=64666 update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer2 remote-address=4.4.4.4 remote-as=64666 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer3 remote-address=5.5.5.5 remote-as=64666 route-reflect=yes update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.LND
</pre></code> 

#### R01.LBN Подключение через sudo ssh admin@172.10.10.4
<pre><code>
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=5.5.5.5
/routing ospf instance
set [ find default=yes ] router-id=5.5.5.5
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.4.40.2/24 interface=ether2 network=172.4.40.0
add address=172.4.60.2/24 interface=ether3 network=172.4.60.0
add address=172.4.70.2/24 interface=ether4 network=172.4.70.0
add address=5.5.5.5 interface=Lo network=5.5.5.5
/ip dhcp-client
add disabled=no interface=ether2
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=3.3.3.3 remote-as=64666 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer3 remote-address=6.6.6.6 remote-as=64666 update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer2 remote-address=4.4.4.4 remote-as=64666 route-reflect=yes update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.HKI
</pre></code>  
  
 #### R01.LND Подключение через sudo ssh admin@172.10.10.5
<pre><code>    
> /interface bridge
> add name=Lo0
> /interface wireless security-profiles
> set [ find default=yes ] supplicant-identity=MikroTik
> /routing ospf instance
> set [ find default=yes ] router-id=4.4.4.4
> /ip address
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
> add address=172.16.3.2/30 interface=ether2 network=172.16.3.0
> add address=172.16.4.1/30 interface=ether3 network=172.16.4.0
> add address=172.16.5.1/30 interface=ether4 network=172.16.5.0
> add address=4.4.4.4/32 interface=Lo0 network=4.4.4.4
> /ip dhcp-client
> add disabled=no interface=ether1
> /mpls ldp
> set enabled=yes transport-address=4.4.4.4
> /mpls ldp interface
> add interface=ether2
> add interface=ether3
> add interface=ether4
> /routing ospf network
> add area=backbone
> /system identity
> set name=R01.HKI  
</code></pre>  

#### R01.SPB Подключение через sudo ssh admin@172.10.10.6  
<pre><code>
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=7.7.7.7
/routing ospf instance
set [ find default=yes ] router-id=7.7.7.7
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.4.50.2/24 interface=ether2 network=172.4.50.0
add address=192.168.20.10/24 interface=ether3 network=192.168.20.0
add address=7.7.7.7 interface=Lo network=7.7.7.7
/ip dhcp-client
add disabled=no interface=ether1
/ip route vrf
add export-route-targets=64666:10 import-route-targets=64666:10 interfaces=ether2 route-distinguisher=64666:10 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp instance vrf
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=4.4.4.4 remote-as=64666 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.SPB
</pre></code>

#### R01.LBN Подключение через sudo ssh admin@172.10.10.7  
<pre><code>
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=5.5.5.5
/routing ospf instance
set [ find default=yes ] router-id=5.5.5.5
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.16.3.2/24 interface=ether2 network=172.16.3.0
add address=172.16.5.2/24 interface=ether3 network=172.16.5.0
add address=172.16.6.1/24 interface=ether4 network=172.16.6.0
add address=5.5.5.5 interface=Lo network=5.5.5.5
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=2.2.2.2 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer2 remote-address=3.3.3.3 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer3 remote-address=6.6.6.6 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.LBN
</pre></code>

#### SGI-Prism Подключение через sudo ssh admin@172.10.10.8  
<pre><code>  
> /interface wireless security-profiles
> set [ find default=yes ] supplicant-identity=MikroTik
> /ip address
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
> add address=192.168.1.10/24 interface=ether2 network=192.168.1.0
> /ip dhcp-client
> add disabled=no interface=ether1
> /system identity
> set name=SGI-Prism
</code></pre>  

#### PC1 Подключение через sudo ssh admin@172.10.10.9
<pre><code> 
> /interface wireless security-profiles
> set [ find default=yes ] supplicant-identity=MikroTik
> /ip address
> add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
> add address=192.168.1.20/24 interface=ether2 network=192.168.1.0
> /ip dhcp-client
> add disabled=no interface=ether1
> /system identity
> set name=PC1
</code></pre> 

#### Скриншоты, показывающие таблицу меток MPLS, приведена ниже: 
Для HKI:  <img width="602" alt="HKI" src="https://user-images.githubusercontent.com/57844480/209515193-08fd0854-e909-4534-a908-24c9ad651a1b.png">
  
Для LBN:  <img width="618" alt="LBN" src="https://user-images.githubusercontent.com/57844480/209515199-36886942-d5fe-419d-af3f-b7bb29fe9fd3.png">

Для LND:  <img width="622" alt="LND" src="https://user-images.githubusercontent.com/57844480/209515209-0abe2a90-ddf0-4052-aee9-efb297070bde.png">

Для MSK:  <img width="607" alt="MSK" src="https://user-images.githubusercontent.com/57844480/209515224-0ae08f3e-b875-45b5-9ded-8fd53f70dd05.png">

Для NY:   <img width="611" alt="NY" src="https://user-images.githubusercontent.com/57844480/209515229-a0f47d71-e415-453e-89a7-a987b1bec386.png">

Для SPB:  <img width="590" alt="SPB" src="https://user-images.githubusercontent.com/57844480/209515236-86707b17-669c-4a75-9e82-15d284baf32d.png">

Настройка VPLS предоставлена ниже:  
Для NY: <img width="623" alt="NY_vpls" src="https://user-images.githubusercontent.com/57844480/209515367-b2eec7f5-1760-40f3-b6ad-0f8163c698f1.png">

Для SPB: <img width="625" alt="SPB_vpls" src="https://user-images.githubusercontent.com/57844480/209515381-d147894c-55c6-42b4-8e62-d48e19ecbf99.png">

Проверка пинга между входом/выхода туннеля: <img width="452" alt="ping" src="https://user-images.githubusercontent.com/57844480/209515434-c1f1155a-e54c-497d-a6a9-c66bf94abd49.png">

Файл конфигурации .yaml вы можете посмотреть [здесь](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab3/lab_3.yaml)  
Схема, начерченная в draw.io:![lab3_scheme](https://user-images.githubusercontent.com/57844480/209515648-cc706040-87bc-4836-93b7-9e937ad2be35.png)
### Особенность, которую хотелось бы отметить:  
#### Пинг возможен только между ПК и сервером. Невозможно следить за "кишками" передачи трафика с конечных устройств, ровно как и с роутеров невозможно смотреть за конечными устройствами, но пинговать другие роутеры можно



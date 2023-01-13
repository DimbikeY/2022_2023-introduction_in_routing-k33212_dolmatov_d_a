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
add disabled=no interface=ether1
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
  
 
#### R01.HKI Подключение через sudo ssh admin@172.10.10.5
<pre><code>
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=4.4.4.4
/routing ospf instance
set [ find default=yes ] router-id=4.4.4.4
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.4.30.2/24 interface=ether2 network=172.4.30.0
add address=172.4.50.1/24 interface=ether3 network=172.4.50.0
add address=172.4.60.1/24 interface=ether4 network=172.4.60.0
add address=4.4.4.4 interface=Lo network=4.4.4.4
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=3.3.3.3 remote-as=64666 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer3 remote-address=7.7.7.7 remote-as=64666 update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer2 remote-address=5.5.5.5 remote-as=64666 route-reflect=yes update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.HKI
</pre></code>  

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

#### R01.SVL Подключение через sudo ssh admin@172.10.10.7  
<pre><code>
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=6.6.6.6
/routing ospf instance
set [ find default=yes ] router-id=6.6.6.6
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.4.70.2/24 interface=ether2 network=172.4.70.0
add address=192.168.30.10/24 interface=ether3 network=192.168.30.0
add address=6.6.6.6 interface=Lo network=6.6.6.6
/ip dhcp-client
add disabled=no interface=ether1
/ip route vrf
add export-route-targets=64666:10 import-route-targets=64666:10 interfaces=ether3 route-distinguisher=64666:10 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp instance vrf
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=5.5.5.5 remote-as=64666 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.SPB
</pre></code>  

### Проверка связности между роутерами:
R01NY: ![ТЫК](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab4/%D0%A1%D0%B2%D1%8F%D0%B7%D0%BD%D0%BE%D1%81%D1%82%D1%8C%20R01.NY%20and%20ping.png)  

R01SPB:![ТЫК](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab4/%D0%A1%D0%B2%D1%8F%D0%B7%D0%BD%D0%BE%D1%81%D1%82%D1%8C%20R01.SPB%20and%20ping.png)  

R01SVL:![ТЫК](https://github.com/DimbikeY/2022_2023-introduction_in_routing-k33212_dolmatov_d_a/blob/main/lab4/%D0%A1%D0%B2%D1%8F%D0%B7%D0%BD%D0%BE%D1%81%D1%82%D1%8C%20R01.SVL%20and%20ping.png)   


#### Скриншоты, показывающие таблицу меток MPLS, приведена ниже: 
  
Схема, начерченная в draw.io:![lab4_scheme]()
### Особенность, которую хотелось бы отметить:  
#### Пинг возможен только между ПК и сервером. Невозможно следить за "кишками" передачи трафика с конечных устройств, ровно как и с роутеров невозможно смотреть за конечными устройствами, но пинговать другие роутеры можно



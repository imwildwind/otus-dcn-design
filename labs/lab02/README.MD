# Построение Underlay сети (OSPF)

## Цели:
1) Настроить OSPF для Underlay сети.
2) Зафиксировать план работ, адресное пространство, схему сети и конфигурацию устройств
3) Убедиться в наличии IP связанности между устройствами в OSPF домене

## План работ:
1) Подготовить конфигурации для топологии 2S3L в соответствии со схемой и IP планом из [лабораторной работы №1](/labs/lab01/README.MD).
2) Развернуть 5 хостов из образа Arista vEOS в Eve-NG.
3) Настроить в соответствии с подготовленными конфигурациями все хосты.
4) Проверить состояние OSPF на всех хостах, таблицы маршрутизации и сетевой связанности до loopback адресов других нод.

## Конфигурации устройств
### Spine1
```eos
! device: Spine1 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine1
!
spanning-tree mode mstp
!
interface Ethernet1
   description Leaf1-eth1
   mtu 9214
   no switchport
   ip address 172.16.1.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Leaf2-eth1
   mtu 9214
   no switchport
   ip address 172.16.2.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description Leaf3-eth1
   mtu 9214
   no switchport
   ip address 172.16.3.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Loopback0
   ip address 10.0.0.1/32
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router ospf 1
   router-id 10.0.0.1
   bfd default
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
!
end
```
### Spine2
```eos
! device: Spine2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine2
!
spanning-tree mode mstp
!
interface Ethernet1
   description Leaf1-eth2
   mtu 9214
   no switchport
   ip address 172.16.1.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Leaf2-eth2
   mtu 9214
   no switchport
   ip address 172.16.2.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description Leaf3-eth2
   mtu 9214
   no switchport
   ip address 172.16.3.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Loopback0
   ip address 10.0.0.2/32
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router ospf 1
   router-id 10.0.0.2
   bfd default
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
!
end
```
### Leaf1
```eos
! device: Leaf1 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf1
!
spanning-tree mode mstp
!
interface Ethernet1
   description Spine1-eth1
   mtu 9214
   no switchport
   ip address 172.16.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine2-eth1
   mtu 9214
   no switchport
   ip address 172.16.1.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet3
!
interface Loopback0
   ip address 10.0.1.1/32
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router ospf 1
   router-id 10.0.1.1
   bfd default
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
end
```
### Leaf2
```eos
! device: Leaf2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf2
!
spanning-tree mode mstp
!
interface Ethernet1
   description Spine1-eth2
   mtu 9214
   no switchport
   ip address 172.16.2.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine2-eth2
   mtu 9214
   no switchport
   ip address 172.16.2.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet3
!
interface Loopback0
   ip address 10.0.1.2/32
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router ospf 1
   router-id 10.0.1.2
   bfd default
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
end
```
### Leaf3
```eos
! device: Leaf3 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf3
!
spanning-tree mode mstp
!
interface Ethernet1
   description Spine1-eth3
   mtu 9214
   no switchport
   ip address 172.16.3.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine2-eth3
   mtu 9214
   no switchport
   ip address 172.16.3.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf authentication-key 7 HIC7kDuPCnCLz/HJBZ68Ng==
   ip ospf area 0.0.0.0
!
interface Ethernet3
!
interface Loopback0
   ip address 10.0.1.3/32
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router ospf 1
   router-id 10.0.1.3
   bfd default
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
end
```
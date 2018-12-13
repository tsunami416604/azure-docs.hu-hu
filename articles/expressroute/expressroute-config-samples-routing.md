---
title: Útválasztó-konfigurációs minták – Azure ExpressRoute |} A Microsoft Docs
description: Ezt oldal útválasztó-konfigurációs minták Cisco és a Juniper útválasztó nyújt.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2d7fb060896de8df266489451a11ba343760c747
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079959"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Kezeli az útválasztást és útválasztó-konfigurációs minták
Ez az oldal nyújt felület és az útválasztási konfigurációs minták Cisco IOS-XE-és a Juniper MX-sorozatú útválasztó az ExpressRoute használata során. Ezek a minták az útmutató csak nem, és nem használható, mert. Használhatja, így kapja meg a hálózat megfelelő konfigurációk a tárolóeszközök gyártójával egyeztetve. 

> [!IMPORTANT]
> Ezen a lapon a minta célja, hogy pusztán az útmutatást. A gyártója által biztosított értékesítési / technikai csapat és a hálózatkezelésért felelős csapat, így kapja meg az igényeinek megfelelő konfigurációt kell működnie. A Microsoft nem támogatja az ezen az oldalon felsorolt konfigurációk kapcsolatos problémákat. Támogatási problémáival kapcsolatban forduljon az eszköz gyártójától.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Útválasztó MTU- és TCP MSS beállításai
* Az ExpressRoute-kapcsolat MTU érték az 1500, a tipikus alapértelmezett MTU egy Ethernet-adapter egy útválasztón. Ha az útválasztó alapértelmezés szerint egy másik MTU van, nincs szükség van az útválasztó-illesztő olyan értéket adjon meg.
* Ellentétben az Azure VPN Gateway a TCP MSS ExpressRoute-kapcsolatcsoport nem kell megadni.

Útválasztó-konfigurációs minták alábbi minden társviszony vonatkoznak. Felülvizsgálat [ExpressRoute-társviszonyok](expressroute-circuit-peerings.md) és [az ExpressRoute útválasztási követelményei](expressroute-routing.md) útválasztás további részleteiért.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE-alapú útválasztók
Ebben a szakaszban a minták az IOS-XE operációsrendszer-család futó bármilyen útválasztó vonatkozik.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. És alárendelt kapcsolatain konfigurálása
Szüksége lesz egy sub felület minden csatlakozik a Microsofthoz útválasztót támaszkodunk. Egy alárendelt felületet egy VLAN-Azonosítót vagy VLAN-azonosítót és a egy IP-cím halmozott párjai azonosíthatók.

**Dot1Q interface definition**

Ez a minta biztosítja az alárendelt illesztőjének definícióját egy alárendelt felületet az egyetlen VLAN-azonosítót. A VLAN-azonosító minden társviszony-létesítés egyedi. Az IPv4-címet, az utolsó oktettet mindig lesz páratlan szám.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ interface definition**

Ez a példa egy két VLAN-azonosító egy alárendelt felületen alárendelt illesztőjének definícióját biztosít. A külső VLAN-Azonosítót (s-címke), ha változatlan marad minden tárviszonyok között. A belső VLAN-Azonosítót (c-címke) minden társviszony-létesítés egyedi. Az IPv4-címet, az utolsó oktettet mindig lesz páratlan szám.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Az eBGP-munkamenetek beállításához
Be kell állítania egy BGP-munkamenet a Microsoft minden társviszony-létesítéshez. Az alábbi minta lehetővé teszi egy BGP-munkamenetet a Microsoft beállítása. Ha az IPv4-címet a sub felület használt a.b.c.d volt, az IP-címet a BGP szomszéd (Microsoft) nem a.b.c.d+1 címet. Az utolsó oktettet az IPv4-címet a BGP-szomszéd mindig lesz páros szám.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. A BGP-munkamenetben meg lehetnek hirdetve az előtagok beállítása
Az útválasztót, jelölje be a Microsoft előtagokat hirdet meg. Megteheti, hogy az alábbi minta használatával.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Útvonal-leképezések
Útvonal-leképezéseket is használhatja és az előtag a szűrő előtagok propagálja a hálózatban. Az alábbi minta használatával a feladatnak. Győződjön meg arról, hogy a megfelelő előtaggal listák beállítása.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX sorozat útválasztók
Ebben a szakaszban a minták bármely Juniper MX-sorozatú útválasztó a alkalmazni.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. És alárendelt kapcsolatain konfigurálása

**Dot1Q interface definition**

Ez a minta biztosítja az alárendelt illesztőjének definícióját egy alárendelt felületet az egyetlen VLAN-azonosítót. A VLAN-azonosító minden társviszony-létesítés egyedi. Az IPv4-címet, az utolsó oktettet mindig lesz páratlan szám.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**QinQ interface definition**

Ez a példa egy két VLAN-azonosító egy alárendelt felületen alárendelt illesztőjének definícióját biztosít. A külső VLAN-Azonosítót (s-címke), ha változatlan marad minden tárviszonyok között. A belső VLAN-Azonosítót (c-címke) minden társviszony-létesítés egyedi. Az IPv4-címet, az utolsó oktettet mindig lesz páratlan szám.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Az eBGP-munkamenetek beállításához
Be kell állítania egy BGP-munkamenet a Microsoft minden társviszony-létesítéshez. Az alábbi minta lehetővé teszi egy BGP-munkamenetet a Microsoft beállítása. Ha az IPv4-címet a sub felület használt a.b.c.d volt, az IP-címet a BGP szomszéd (Microsoft) nem a.b.c.d+1 címet. Az utolsó oktettet az IPv4-címet a BGP-szomszéd mindig lesz páros szám.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. A BGP-munkamenetben meg lehetnek hirdetve az előtagok beállítása
Az útválasztót, jelölje be a Microsoft előtagokat hirdet meg. Megteheti, hogy az alábbi minta használatával.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Útvonal-leképezések
Útvonal-leképezéseket is használhatja és az előtag a szűrő előtagok propagálja a hálózatban. Az alábbi minta használatával a feladatnak. Győződjön meg arról, hogy a megfelelő előtaggal listák beállítása.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>További lépések
További részletek: [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – gyakori kérdések).


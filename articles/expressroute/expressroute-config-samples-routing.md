---
title: ExpressRoute ügyfél útválasztó konfigurációs minták |} Microsoft Docs
description: Ezen a lapon útválasztó config minták biztosít a Cisco és a Juniper útválasztó.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850769"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Útválasztó beállítása és kezelése az útválasztási konfigurációs minták
Ezen a lapon Cisco IOS-XE és a Juniper MX adatsorozat útválasztók felületet és útválasztási konfigurációs minták biztosít. Ezek a minták csak útmutatót kell, és nem kell használni, mert a. A gyártó, így kapja meg a hálózat megfelelő konfigurációi dolgozhat. 

> [!IMPORTANT]
> Ezen a lapon minták célja, hogy pusztán az útmutatást kell. A gyártója által biztosított értékesítés / műszaki adapterek és a hálózati így kapja meg az igényeinek megfelelő konfigurációt kell dolgozni. A Microsoft nem támogatja a beállításokat ezen a lapon szereplő kapcsolatos problémákat. Támogatási kérdéseivel forduljon az eszköz gyártója.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Útválasztó-kapcsolatokon MTU-beállítása és TCP MSS beállításai
* A MTU-beállítása, a ExpressRoute kapcsolat 1500, a tipikus alapértelmezett MTU-beállítása az Ethernet-adapter egy útválasztón. Az útválasztó alapértelmezés szerint van egy másik MTU-beállítása, hacsak nincs szükség az érték határozza meg az útválasztó-illesztő.
* Az Azure VPN Gateway eltérően a TCP MSS ExpressRoute-kör nem kell megadni.

Útválasztó-konfiguráció minták az alábbi összes esetében érvényesek. Felülvizsgálati [ExpressRoute-társviszony](expressroute-circuit-peerings.md) és [ExpressRoute útválasztási követelmények](expressroute-routing.md) útválasztási olvashat.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE-alapú útválasztók
Ebben a szakaszban a minták bármely útválasztóját az IOS-XE operációsrendszer-család érvényes.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. És alárendelt kapcsolatain konfigurálása
Szüksége lesz egy sub felületet / minden útválasztót csatlakozhat a Microsoft társviszony-létesítés. Egy sub felületet azonosítható a VLAN-azonosító vagy halmozott két virtuális helyi hálózati azonosítókat és az IP-címet.

**Dot1Q felületdefiníció**

Ez a minta biztosítja a alárendelt felületdefiníció egy alárendelt felület az egyetlen VLAN-azonosítót. A VLAN-azonosító minden társviszony-létesítés egyedi. Az IPv4-cím utolsó oktettje mindig lesz páratlan szám.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ felületdefiníció**

Ez a minta biztosítja a alárendelt felületdefiníció egy alárendelt felület egy két virtuális helyi hálózati azonosítóval. A külső VLAN-azonosító (s-címke), ha változatlan marad, a társviszony között. A belső VLAN-azonosító (c-címke) társviszony-létesítés minden egyedi. Az IPv4-cím utolsó oktettje mindig lesz páratlan szám.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. EBGP munkamenetek beállítása
Be kell állítania egy BGP-munkamenetet a Microsoft az a minden társviszony-létesítéshez. Az alábbi minta egy BGP-munkamenetet a Microsoft telepítési teszi lehetővé. Ha a sub felület használt IPv4-cím volt a.b.c.d, az IP-címet a BGP szomszéd (Microsoft) nem a.b.c.d+1. A BGP szomszéd IPv4-cím utolsó oktettje mindig lesz páros szám.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. A BGP-munkameneten keresztül hirdetését előtagok beállítása
Az útválasztót Microsoft válassza előtagok hivatkozik. Ehhez használja az alábbi minta.

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
Útvonal-leképezések is használhat, és előtag sorolja fel, a szűrő-előtagok a hálózaton történő propagálása. Az alábbi minta segítségével a feladatnak. Győződjön meg arról, hogy rendelkezik a megfelelő előtaggal listák beállítása.

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


## <a name="juniper-mx-series-routers"></a>Juniper MX adatsorozat útválasztók
Ebben a szakaszban a minták Juniper MX adatsorozat útválasztókkal érvényes.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. És alárendelt kapcsolatain konfigurálása

**Dot1Q felületdefiníció**

Ez a minta biztosítja a alárendelt felületdefiníció egy alárendelt felület az egyetlen VLAN-azonosítót. A VLAN-azonosító minden társviszony-létesítés egyedi. Az IPv4-cím utolsó oktettje mindig lesz páratlan szám.

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


**QinQ felületdefiníció**

Ez a minta biztosítja a alárendelt felületdefiníció egy alárendelt felület egy két virtuális helyi hálózati azonosítóval. A külső VLAN-azonosító (s-címke), ha változatlan marad, a társviszony között. A belső VLAN-azonosító (c-címke) társviszony-létesítés minden egyedi. Az IPv4-cím utolsó oktettje mindig lesz páratlan szám.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. EBGP munkamenetek beállítása
Be kell állítania egy BGP-munkamenetet a Microsoft az a minden társviszony-létesítéshez. Az alábbi minta egy BGP-munkamenetet a Microsoft telepítési teszi lehetővé. Ha a sub felület használt IPv4-cím volt a.b.c.d, az IP-címet a BGP szomszéd (Microsoft) nem a.b.c.d+1. A BGP szomszéd IPv4-cím utolsó oktettje mindig lesz páros szám.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. A BGP-munkameneten keresztül hirdetését előtagok beállítása
Az útválasztót Microsoft válassza előtagok hivatkozik. Ehhez használja az alábbi minta.

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
Útvonal-leképezések is használhat, és előtag sorolja fel, a szűrő-előtagok a hálózaton történő propagálása. Az alábbi minta segítségével a feladatnak. Győződjön meg arról, hogy rendelkezik a megfelelő előtaggal listák beállítása.

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

## <a name="next-steps"></a>Következő lépések
További részletek: [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – gyakori kérdések).


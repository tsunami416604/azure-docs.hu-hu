---
title: 'Azure ExpressRoute: Útválasztó konfigurációs mintái'
description: Ez az oldal router konfigurációs mintákat biztosít a Cisco és a Juniper útválasztók számára.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: 2c37dadeb669fb88f858b5487379828a8dddec6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076667"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Útválasztó konfigurációs mintái az útválasztás beállításához és kezeléséhez
Ez a lap összeköttetés- és útválasztási konfigurációs mintákat biztosít a Cisco IOS-XE és juniper MX sorozatú útválasztók számára az ExpressRoute használata során. Ezek csak útmutatásul szolgáló minták, és nem használhatók a tervek szerint. A szállítóval együttműködve kidolgozhat ja a hálózatmegfelelő konfigurációit. 

> [!IMPORTANT]
> Az ezen az oldalon található minták kizárólag útmutatást szolgálnak. Meg kell dolgozni a szállító értékesítési / műszaki csapat és a hálózati csapat, hogy dolgozzon ki a megfelelő konfigurációkat, hogy megfeleljen az Ön igényeinek. A Microsoft nem támogatja az ezen a lapon felsorolt konfigurációkkal kapcsolatos problémákat. Támogatási problémák esetén forduljon az eszköz forgalmazójához.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>MTU- és TCP MSS-beállítások az útválasztó-összeköttetéseken
* Az ExpressRoute-kapcsolat MTU-ja 1500, amely az útválasztó Ethernet-összeköttetésének tipikus alapértelmezett MTU-ja. Ha az útválasztó alapértelmezés szerint nem rendelkezik másik MTU-val, nincs szükség érték megadására az útválasztó-kapcsolaton.
* Az Azure VPN-átjáróval ellentétben az ExpressRoute-kapcsolati kapcsolatt nem kell megadni.

Az útválasztó-konfigurációs minták az összes társtársi kapcsolatra vonatkoznak. Tekintse át [az ExpressRoute-társviszony-létesítési](expressroute-circuit-peerings.md) és [expressroute-útválasztási követelményeket](expressroute-routing.md) az útválasztástovábbi részleteiért.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE alapú útválasztók
Az ebben a szakaszban szereplő minták az IOS-XE operációs rendszercsaládot futtató útválasztókra vonatkoznak.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Kapcsolódási pontok és alinterfészek konfigurálása
A Microsofthoz csatlakozó útválasztókon társviszonyban egy alillesztésre lesz szüksége. Az alkapcsolat VLAN-azonosítóval vagy vlan-azonosítók halmozott párjával és IP-címmel azonosítható.

**Dot1Q illesztő definíciója**

Ez a minta egyetlen VLAN-azonosítóval rendelkező alillesztő-alapú felület alillesztő-definícióját tartalmazza. A VLAN-azonosító társviszony-lemunkálásonként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ-csatoló definíciója**

Ez a minta két VLAN-azonosítóval rendelkező alillesztő-kapcsolat alillesztő-definícióját tartalmazza. A külső VLAN-azonosító (s-tag), ha használják ugyanaz marad az összes társviszony-létesítések. A belső VLAN-azonosító (c-tag) társviszony-létesítésenként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. EBGP-ülések létrehozása
Minden társviszony-létesítéshez be kell állítania egy BGP-munkamenetet a Microsofttal. Az alábbi minta lehetővé teszi a BGP-munkamenet microsoftos beállítását. Ha az alkapcsolathoz használt IPv4-cím a.b.c.d volt, a BGP-szomszéd (Microsoft) IP-címe a.b.c.d+1 lesz. A BGP-szomszéd IPv4-címének utolsó oktettje mindig páros szám lesz.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. A BGP-ülésen meghirdetendő előtagok beállítása
Beállíthatja, hogy az útválasztó bizonyos előtagokat hirdessen a Microsoft számára. Ezt az alábbi minta segítségével teheti meg.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Útvonaltérképek
Az útvonalleképezések és előtagok listái segítségével szűrheti a hálózatra propagált előtagokat. Az alábbi minta segítségével elvégezheti a feladatot. Győződjön meg arról, hogy rendelkezik a megfelelő előtaglisták beállításával.

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


## <a name="juniper-mx-series-routers"></a>Juniper MX sorozatú útválasztók
Az ebben a szakaszban szereplő minták minden Juniper MX sorozatú útválasztóra vonatkoznak.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Kapcsolódási pontok és alinterfészek konfigurálása

**Dot1Q illesztő definíciója**

Ez a minta egyetlen VLAN-azonosítóval rendelkező alillesztő-alapú felület alillesztő-definícióját tartalmazza. A VLAN-azonosító társviszony-lemunkálásonként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

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


**QinQ-csatoló definíciója**

Ez a minta két VLAN-azonosítóval rendelkező alillesztő-kapcsolat alillesztő-definícióját tartalmazza. A külső VLAN-azonosító (s-tag), ha használják ugyanaz marad az összes társviszony-létesítések. A belső VLAN-azonosító (c-tag) társviszony-létesítésenként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. EBGP-ülések létrehozása
Minden társviszony-létesítéshez be kell állítania egy BGP-munkamenetet a Microsofttal. Az alábbi minta lehetővé teszi a BGP-munkamenet microsoftos beállítását. Ha az alkapcsolathoz használt IPv4-cím a.b.c.d volt, a BGP-szomszéd (Microsoft) IP-címe a.b.c.d+1 lesz. A BGP-szomszéd IPv4-címének utolsó oktettje mindig páros szám lesz.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. A BGP-ülésen meghirdetendő előtagok beállítása
Beállíthatja, hogy az útválasztó bizonyos előtagokat hirdessen a Microsoft számára. Ezt az alábbi minta segítségével teheti meg.

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


### <a name="4-route-maps"></a>4. Útvonaltérképek
Az útvonalleképezések és előtagok listái segítségével szűrheti a hálózatra propagált előtagokat. Az alábbi minta segítségével elvégezheti a feladatot. Győződjön meg arról, hogy rendelkezik a megfelelő előtaglisták beállításával.

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


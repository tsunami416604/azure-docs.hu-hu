---
title: 'Azure ExpressRoute: Útválasztó konfigurációs mintái'
description: Ez az oldal router konfigurációs mintákat biztosít a Cisco és a Juniper útválasztók számára.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024812"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Útválasztó konfigurációs mintái az útválasztás beállításához és kezeléséhez
Ez a lap az Azure ExpressRoute használata során a Cisco IOS-XE és a Juniper MX sorozatú útválasztók kapcsolati és útválasztási konfigurációs mintáit tartalmazza.

> [!IMPORTANT]
> Az ezen az oldalon található minták kizárólag útmutatást szolgálnak. A gyártó értékesítési/műszaki csapatával és a hálózati csapattal együtt kell működnie, hogy megtalálja az igényeinek megfelelő konfigurációkat. A Microsoft nem támogatja az ezen a lapon felsorolt konfigurációkkal kapcsolatos problémákat. Támogatási problémákesetén forduljon az eszköz forgalmazójához.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>MTU- és TCP MSS-beállítások az útválasztó-összeköttetéseken
Az ExpressRoute-kapcsolat maximális átviteli egysége (MTU) az 1500, amely az útválasztó Ethernet-összeköttetésének tipikus alapértelmezett MTU-ja. Ha az útválasztó alapértelmezés szerint nem rendelkezik másik MTU-val, nincs szükség érték megadására az útválasztó-kapcsolaton.

Az Azure VPN-átjáróval ellentétben az ExpressRoute-kapcsolatoni tcp-szegmensméretet (MSS) nem kell megadni.

A cikkben szereplő útválasztó-konfigurációs minták az összes társviszony-létesítésre vonatkoznak. Tekintse át [az ExpressRoute-társviszony-létesítési](expressroute-circuit-peerings.md) és [expressroute-útválasztási követelményeket](expressroute-routing.md) az útválasztástovábbi részleteiért.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE alapú útválasztók
Az ebben a szakaszban szereplő minták az IOS-XE operációs rendszercsaládot futtató útválasztókra vonatkoznak.

### <a name="configure-interfaces-and-subinterfaces"></a>Összeköttetések és alillesztők konfigurálása
Minden útválasztóban, amelyhez a Microsofthoz csatlakozik, társviszonyonként egy alfelületre lesz szüksége. Az alfelület VLAN-azonosítóval vagy vlan-azonosítók halmozott párjával és IP-címmel azonosítható.

**Dot1Q illesztő definíciója**

Ez a minta egyetlen VLAN-azonosítóval rendelkező alfelület alfelület-definícióját tartalmazza. A VLAN-azonosító társviszony-lemunkálásonként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ-csatoló definíciója**

Ez a minta két VLAN-azonosítóval rendelkező alfelület alfelület-definícióját tartalmazza. A külső VLAN-azonosító (s-tag), ha használják, ugyanaz marad az összes társviszony-létesítések. A belső VLAN-azonosító (c-tag) társviszony-létesítésenként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>EBGP-munkamenetek beállítása
Minden társviszony-létesítéshez be kell állítania egy BGP-munkamenetet a Microsofttal. BGP-munkamenet beállítása az alábbi minta használatával. Ha az alkapcsolathoz használt IPv4-cím a.b.c.d volt, akkor a BGP-szomszéd (Microsoft) IP-címe a.b.c.d+1 lesz. A BGP-szomszéd IPv4-címének utolsó oktettje mindig páros szám lesz.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>A BGP-munkameneten keresztül hirdetendő előtagok beállítása
Konfigurálja úgy az útválasztót, hogy a következő minta segítségével hirdesse a microsoftos előtagokat.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Útvonaltérképek
Útvonalleképezések és előtagok listáinak használatával szűrheti a hálózatra propagált előtagokat. Tekintse meg a következő mintát, és győződjön meg arról, hogy a megfelelő előtaglisták vannak beállítva.

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

### <a name="configure-bfd"></a>Bfd konfigurálása

A BFD-t két helyen kell konfigurálni: az egyik a kapcsolat szintjén, a másik bgp szinten. A példa itt a QinQ felület. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX sorozatú útválasztók
Az ebben a szakaszban szereplő minták minden Juniper MX sorozatú útválasztóra vonatkoznak.

### <a name="configure-interfaces-and-subinterfaces"></a>Összeköttetések és alillesztők konfigurálása

**Dot1Q illesztő definíciója**

Ez a minta egyetlen VLAN-azonosítóval rendelkező alfelület alfelület-definícióját tartalmazza. A VLAN-azonosító társviszony-lemunkálásonként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

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

Ez a minta két VLAN-azonosítóval rendelkező alfelület alfelület-definícióját tartalmazza. A külső VLAN-azonosító (s-tag), ha használják, ugyanaz marad az összes társviszony-létesítések. A belső VLAN-azonosító (c-tag) társviszony-létesítésenként egyedi. Az IPv4-cím utolsó oktettje mindig páratlan szám lesz.

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

### <a name="set-up-ebgp-sessions"></a>EBGP-munkamenetek beállítása
Minden társviszony-létesítéshez be kell állítania egy BGP-munkamenetet a Microsofttal. BGP-munkamenet beállítása az alábbi minta használatával. Ha az alkapcsolathoz használt IPv4-cím a.b.c.d volt, akkor a BGP-szomszéd (Microsoft) IP-címe a.b.c.d+1 lesz. A BGP-szomszéd IPv4-címének utolsó oktettje mindig páros szám lesz.

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>A BGP-munkameneten keresztül hirdetendő előtagok beállítása
Konfigurálja úgy az útválasztót, hogy a következő minta segítségével hirdesse a microsoftos előtagokat.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
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


### <a name="route-policies"></a>Útvonalházirendek
Az útvonalleképezések és előtagok listái segítségével szűrheti a hálózatra propagált előtagokat. Tekintse meg a következő mintát, és győződjön meg arról, hogy a megfelelő előtaglisták be vannak állítva.

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

### <a name="configure-bfd"></a>Bfd konfigurálása
Csak a BGP protokoll szakaszban konfigurálja a BFD-t.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>További lépések
További részletek: [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – gyakori kérdések).




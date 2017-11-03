---
title: "ExpressRoute ügyfél útválasztó konfigurációs minták |} Microsoft Docs"
description: "Ezen a lapon útválasztó-konfiguráció minták biztosít a Cisco és a Juniper útválasztó."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: d6ea716f-d5ee-4a61-92b0-640d6e7d6974
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 83a7da2db537a3c900e90432455d59e8ac56d917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Útválasztó beállítása és kezelése NAT konfigurációs minták
Ezen a lapon NAT konfigurációs minták biztosít a Cisco ASA és a Juniper SRX adatsorozat útválasztó. Ezek a minták csak útmutatót kell, és nem kell használni, mert a. A gyártó, így kapja meg a hálózat megfelelő konfigurációi dolgozhat. 

> [!IMPORTANT]
> Ezen a lapon minták célja, hogy pusztán az útmutatást kell. A gyártója által biztosított értékesítés / műszaki adapterek és a hálózati így kapja meg az igényeinek megfelelő konfigurációt kell dolgozni. A Microsoft nem támogatja a beállításokat ezen a lapon szereplő kapcsolatos problémákat. Támogatási kérdéseivel forduljon az eszköz gyártója.
> 
> 

* Útválasztó-konfiguráció minták az alábbi Azure nyilvános és a Microsoft társviszony vonatkozik. Az Azure magánhálózati társviszony-létesítés nem NAT kell konfigurálni. Felülvizsgálati [ExpressRoute-társviszony](expressroute-circuit-peerings.md) és [ExpressRoute NAT követelmények](expressroute-nat.md) további részleteket.

* Külön hálózati Címfordítás IP-címkészletek az internetes és az ExpressRoute kapcsolattal kell használnia. Az azonos hálózati Címfordítás IP-címkészlet használata között, az internetes és az ExpressRoute aszimmetrikus az Útválasztás és a kapcsolat megszakadása eredményez.


## <a name="cisco-asa-firewalls"></a>Cisco ASA tűzfal
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Microsoft-ügyfelek hálózati forgalom PAT konfigurációja
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>A Microsoft ügyfél-hálózat felé irányuló forgalom PAT konfigurációja

**Felületek és iránya:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**A konfiguráció:**

NAT-készlete:

    object network outbound-PAT
        host <NAT-IP>

Célkiszolgáló:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Az ügyfél IP-címhez objektum-csoport

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT-parancsok:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX adatsorozat útválasztók
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Redundáns Ethernet-illesztőkhöz használható a fürt létrehozása
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Hozzon létre két biztonsági zónák
* Megbízhatósági kapcsolat a belső hálózathoz és zónát Untrust külső hálózat felé néző peremhálózati útválasztók
* A zónák megfelelő felületek hozzárendelése
* Az interfészen-szolgáltatások engedélyezése

    biztonsági {zónák {biztonsági-zóna megbízhatósági {állomás bejövő-forgalom {-rendszerszolgáltatások {ping;                   } {bgp; protokollok                   {reth0.100;}} felületek               }} biztonsági-zóna Untrust {állomás bejövő-forgalom {-rendszerszolgáltatások {ping;                   } {bgp; protokollok                   {reth1.100;}} felületek               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Biztonsági házirendek közötti zónák létrehozása
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. NAT-szabályzatok konfigurálása
* Hozzon létre két NAT-készletek. Egy használandó NAT-forgalom kimenő Microsoft és más Microsoft az ügyfélnek.
* NAT-szabályok létrehozása a megfelelő forgalom
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Mindkét irányban szelektív előtagok hivatkozik BGP konfigurálása
Tekintse meg a minták [útválasztás konfigurációs minták ](expressroute-config-samples-routing.md) lap.

### <a name="6-create-policies"></a>6. Házirendek létrehozása
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Következő lépések
További részletek: [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – gyakori kérdések).


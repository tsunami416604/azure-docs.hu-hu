---
title: Útválasztó-konfigurációs minták az Azure ExpressRoute - NAT - |} A Microsoft Docs
description: Ezt oldal útválasztó-konfigurációs minták Cisco és a Juniper útválasztó nyújt.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: ccee0f0c01119ebbfb5ba9c5980ee006a555a399
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872080"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Útválasztó-konfigurációs minták beállításához és kezeléséhez NAT

Ez az oldal konfigurációs minták NAT nyújt a Cisco ASA és a Juniper SRX sorozatú útválasztó az ExpressRoute használatakor. Ezek a minták az útmutató csak nem, és nem használható, mert. Használhatja, így kapja meg a hálózat megfelelő konfigurációk a tárolóeszközök gyártójával egyeztetve.

> [!IMPORTANT]
> Ezen a lapon a minta célja, hogy pusztán az útmutatást. A gyártója által biztosított értékesítési / technikai csapat és a hálózatkezelésért felelős csapat, így kapja meg az igényeinek megfelelő konfigurációt kell működnie. A Microsoft nem támogatja az ezen az oldalon felsorolt konfigurációk kapcsolatos problémákat. Támogatási problémáival kapcsolatban forduljon az eszköz gyártójától.
> 
> 

* Az alábbi az útválasztó-konfigurációs minták az Azure nyilvános és Microsoft társviszony-létesítéseket vonatkoznak. Nem az Azure privát társviszony-létesítés NAT kell konfigurálnia. Felülvizsgálat [ExpressRoute-társviszonyok](expressroute-circuit-peerings.md) és [ExpressRoute NAT-követelmények](expressroute-nat.md) további részletekért.

* A csatlakozást és az ExpressRoute NAT IP-készletek külön kell használnia. Között és az ExpressRoute NAT IP-készlet használatával aszimmetrikus útválasztást és a kapcsolat megszakadását eredményezi.


## <a name="cisco-asa-firewalls"></a>Cisco ASA tűzfalak
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PAT konfigurációját és a Microsoft az ügyfél hálózati forgalom
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

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>A Microsoft hálózati ügyfél felé irányuló forgalom PAT-konfiguráció

**Felületek és iránya:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Konfiguráció:**

NAT-készletet:

    object network outbound-PAT
        host <NAT-IP>

Célkiszolgáló:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Ügyfél IP-cím objektum-csoport

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT-parancsokat:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX sorozat útválasztók
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Redundáns Ethernet-illesztőkhöz a fürt létrehozása
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


### <a name="2-create-two-security-zones"></a>2. Hozzon létre két biztonsági zóna
* Megbízhatósági kapcsolat a belső hálózathoz és Untrust zónába peremhálózati útválasztói rendelkező külső hálózat
* A zónák megfelelő adapterek hozzárendelése
* A kapcsolatok-szolgáltatások engedélyezése

    a biztonsági zónák {{biztonsági-zóna megbízhatósági {gazdagép-bejövő-forgalom {-rendszerszolgáltatások {ping;                   } {bgp; protokollok                   {reth0.100;}} felületek               }} {gazdagép-bejövő-forgalom {-rendszerszolgáltatások {ping; biztonsági-zóna Untrust                   } {bgp; protokollok                   {reth1.100;}} felületek               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Zónák közötti biztonsági házirendek létrehozása
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
* Hozzon létre két NAT-készletek. Egy használandó kimenő, a Microsoft és más NAT-forgalom a Microsoft az ügyfél számára.
* A NAT-szabályok létrehozása a megfelelő forgalmat
  
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

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Az összes csatlakozás minden irányában szelektív előtagokat hirdet meg a BGP konfigurálása
Tekintse meg a mintákat [útválasztás konfigurációs minták](expressroute-config-samples-routing.md) lapot.

### <a name="6-create-policies"></a>6. Szabályzatok létrehozása
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

## <a name="next-steps"></a>További lépések
További részletek: [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – gyakori kérdések).


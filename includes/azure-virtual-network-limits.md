---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 02/07/2019
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: fe1993a914ea4d3bd8ab9116748198cbb0c1c43c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905221"
---
<a name="virtual-networking-limits-classic"></a>Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése előfizetésenként, a klasszikus üzemi modellel történik. Ismerje meg, hogyan [megtekintheti az aktuális erőforrás-használat elleni az előfizetési korlátok](../articles/networking/check-usage-against-limits.md).

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |100 |
| Helyi hálózati helyek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |20 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |4096 |
| Egyidejű TCP vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány adapterenként |500 000 |500 000 |
| Hálózati biztonsági csoportok (NSG) |100 |200 |
| NSG-ben szereplő NSG-szabályok |200 |1000 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |100 |400 |
| Nyilvános IP-címek (dinamikus) |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Fenntartott nyilvános IP-címek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános virtuális IP-címek száma üzemelő példányonként |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Privát virtuális IP-címek (ILB) száma üzemelő példányonként |1 |1 |
| Végponthozzáférés-vezérlési listák (ACL-ek) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Hálózatkezelési korlátok – Azure Resource Manager
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Ismerje meg, hogyan [megtekintheti az aktuális erőforrás-használat elleni az előfizetési korlátok](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> A Microsoft a közelmúltban növelte az összes alapértelmezett korlátok a maximális korlátig való. Ha nincs **maximálisan** oszlop, akkor az erőforrás nem állítható korlátokkal rendelkeznek. Ha ezeket a korlátokat, támogatja a múltban nagyobb volt, és nem látható frissített korlátait az alábbi, [nyisson meg egy online támogatási kérést díj nélkül](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Erőforrás | Alapértelmezett korlát | 
| --- | --- |
| Virtuális hálózatok |1000 |
| Alhálózatok száma virtuális hálózatonként |3000 |
| Virtuális hálózati társviszony-létesítések száma virtuális hálózatonként |100 |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |
| Magánhálózati IP-címek száma virtuális hálózatonként |65536 |
| Magánhálózati IP-címek száma hálózati adapterenként |256 |
| Magánhálózati IP-címek virtuális gépenként |256 |
| Egyidejű TCP vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány adapterenként |500 000 |
| Hálózati adapterek (NIC) |65536 |
| Hálózati biztonsági csoportok (NSG) |5000 |
| NSG-ben szereplő NSG-szabályok |1000 |
| IP-címek és a forrás vagy cél egyetlen biztonsági csoportba tartozó megadott tartományok |4000 |
| Alkalmazásbiztonsági csoportok |3000 |
| Az alkalmazásbiztonsági csoportok száma / hálózati adapter IP-Címének konfigurálása |20 |
| IP-konfigurációk száma alkalmazásbiztonsági csoport |4000 |
| Az alkalmazásbiztonsági csoportok belül az összes biztonsági szabályok egy hálózati biztonsági csoport adható meg |100 |
| Felhasználó által megadott útvonaltáblák |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |400 |
| Pont–hely kapcsolati főtanúsítványok száma VPN Gateway-példányonként |20 |
| Virtuális hálózat koppint |100 |
| Hálózati adapter KOPPINTSON konfigurációk száma virtuális hálózatonként KOPPINTSON |100 |

#### <a name="publicip-address"></a>Nyilvános IP-cím korlátok
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek – dinamikus |1000 (alapszintű) |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |200-as (alapszintű) |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |(Standard) 200 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-előtag méretét (előzetes verzió) | /28 | /28 |

#### <a name="load-balancer"></a>Load Balancer korlátok
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Ismerje meg, hogyan [az aktuális erőforrás-használat az előfizetés korlátozások megtekintése](../articles/networking/check-usage-against-limits.md)

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Terheléselosztók | 1000 | 
| Erőforrásonként alapszintű szabályok | 250 |
| Erőforrásonként Standard szabályok | 1500 | 
| Szabályok száma IP-konfiguráció | 299 |
| Szabályok száma hálózati adapter | 500 |
| Előtérbeli IP-konfigurációk, alapszintű | 200 |
| Előtérbeli IP-konfigurációk, Standard | 600 |
| Háttérkészlet, alapszintű | 100, egy rendelkezésre állási csoport |
| Háttérkészlet, a Standard | 1000, egyetlen virtuális hálózaton |
| Háttérerőforrásokhoz Load Balancer Standard / * | 150 |
| HA Ports, Standard | 1 / belső frontend |

** Fel a 150 erőforrásokhoz, önálló virtuálisgép-erőforrások tetszőleges kombinációjának rendelkezésre állási csoportok erőforrásokat, és virtuálisgép-méretezési csoport állítsa erőforrásokat.


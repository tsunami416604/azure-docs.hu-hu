---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 08/03/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 9dbd230e431b2bc3041cfc854c0161ce8d453870
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40133171"
---
<a name="virtual-networking-limits-classic"></a>Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése előfizetésenként, a klasszikus üzemi modellel történik. Ismerje meg, hogyan [megtekintheti az aktuális erőforrás-használat elleni az előfizetési korlátok](../articles/networking/check-usage-against-limits.md).

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |100 |
| Helyi hálózati helyek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |100 |
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
| DNS-kiszolgálók száma virtuális hálózatonként |25 |
| Magánhálózati IP-címek száma virtuális hálózatonként |a 65536 értékű |
| Magánhálózati IP-címek száma hálózati adapterenként |256 |
| Egyidejű TCP vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány adapterenként |500 000 |
| Hálózati adapterek (NIC) |24000 |
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

#### <a name="publicip-address"></a>Nyilvános IP-cím korlátok

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek – dinamikus |200-as (alapszintű) |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |200-as (alapszintű) |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |200-as (standard) |kapcsolatfelvétel az ügyfélszolgálattal |

#### <a name="load-balancer"></a>Load Balancer korlátok
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Ismerje meg, hogyan [az aktuális erőforrás-használat az előfizetés korlátozások megtekintése](../articles/networking/check-usage-against-limits.md)

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Terheléselosztók | 100 | 1000 |
| Erőforrásonként alapszintű szabályok | 150 | 250 |
| Erőforrásonként Standard szabályok | 1250 | 1500 |
| Szabályok száma IP-konfiguráció | 299 |299 |
| Előtérbeli IP-konfigurációk, alapszintű | 10 | 200 |
| Előtérbeli IP-konfigurációk, Standard | 10 | 600 |
| Háttérkészlet, alapszintű | 100, egy rendelkezésre állási csoport | 100, egy rendelkezésre állási csoport |
| Háttérkészlet, a Standard | 1000, egyetlen virtuális hálózaton | 1000, egyetlen virtuális hálózaton |
| Load Balancer Standard / háttérerőforrásokhoz &ast; | 50 | 150 |
| Magas rendelkezésre ÁLLÁS portok, a Standard | 1 / belső frontend | 1 / belső frontend |

&ast; Legfeljebb 150 erőforrásokat, önálló virtuális gépek rendelkezésre állási csoportok és a virtual machine scale sets tetszőleges kombinációját.

Ha meg szeretné növelni a korlátozások alapértelmezett értékeit, [lépjen kapcsolatba az ügyfélszolgálattal](../articles/azure-supportability/resource-manager-core-quotas-request.md ).


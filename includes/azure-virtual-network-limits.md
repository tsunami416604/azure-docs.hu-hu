---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 06/20/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 9ba9bc993832350f6b6ce1c642e2dc852731b6f0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39029971"
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

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |1000 |
| Alhálózatok száma virtuális hálózatonként |1000 |10000 |
| Virtuális hálózati társviszony-létesítések száma virtuális hálózatonként |50 ** |100 |
| DNS-kiszolgálók száma virtuális hálózatonként |9 |25 |
| Magánhálózati IP-címek száma virtuális hálózatonként |16384 ** |16384 |
| Magánhálózati IP-címek száma hálózati adapterenként |256 |256 |
| Egyidejű TCP vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány adapterenként |500 000 |500 000 |
| Hálózati adapterek (NIC) |24000 ** |24000 |
| Hálózati biztonsági csoportok (NSG) |100 |5000 |
| NSG-ben szereplő NSG-szabályok |1000 ** |1000 |
| IP-címek és a forrás vagy cél egyetlen biztonsági csoportba tartozó megadott tartományok |2000 |4000 |
| Alkalmazásbiztonsági csoportok |500 |3000 |
| Az alkalmazásbiztonsági csoportok száma / hálózati adapter IP-Címének konfigurálása |10 |20 |
| IP-konfigurációk száma alkalmazásbiztonsági csoport |1000 |4000 |
| Az alkalmazásbiztonsági csoportok belül az összes biztonsági szabályok egy hálózati biztonsági csoport adható meg |50 |100 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |400 ** |400 |
| Nyilvános IP-címek – dinamikus |(Basic) 60 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |(Basic) 20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |(Standard) 20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Pont–hely kapcsolati főtanúsítványok száma VPN Gateway-példányonként |20 |20 |

** A frissített alapértelmezett korlátok vonatkoznak az előfizetésekre, amelyek korábban nem volt ezeket a korlátokat támogatásának nőtt. Ha rendelkezik növelni a múltban támogatja ezeket a korlátokat, és szeretne kapni az új alapértelmezett frissítését [nyisson meg egy online támogatási kérést díj nélkül](../articles/azure-resource-manager/resource-manager-quota-errors.md)

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


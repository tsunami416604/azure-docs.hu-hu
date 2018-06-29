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
ms.openlocfilehash: 326da32f91b263bbd09a4c6f521c9ec72094820c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066067"
---
<a name="virtual-networking-limits-classic"></a>Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése előfizetésenként, a klasszikus üzemi modellel történik. Megtudhatja, hogyan [megtekintheti az aktuális erőforrás-használata az előfizetés korlátozások](../articles/networking/check-usage-against-limits.md).

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |100 |
| Helyi hálózati helyek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |100 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |4096 |
| Egyidejű TCP vagy UDP-forgalmat egy hálózati Adapterének virtuális gép vagy szerepkörpéldány |500 000 |500 000 |
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
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Megtudhatja, hogyan [megtekintheti az aktuális erőforrás-használata az előfizetés korlátozások](../articles/networking/check-usage-against-limits.md).

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |1000 |
| Alhálózatok száma virtuális hálózatonként |1000 |10000 |
| Virtuális hálózati társviszony virtuális hálózatonként |10 |50 |
| DNS-kiszolgálók száma virtuális hálózatonként |9 |25 |
| Magánhálózati IP-címek száma virtuális hálózatonként |16384 ** |16384 |
| Magánhálózati IP-címek száma hálózati adapterenként |256 |256 |
| Egyidejű TCP vagy UDP-forgalmat egy hálózati Adapterének virtuális gép vagy szerepkörpéldány |500 000 |500 000 |
| Hálózati adapterek (NIC) |24000 ** |24000 |
| Hálózati biztonsági csoportok (NSG) |100 |5000 |
| NSG-ben szereplő NSG-szabályok |1000 ** |1000 |
| IP-címek és egy biztonsági csoportot a cél- és a megadott tartományok |2000 |4000 |
| Alkalmazásbiztonsági csoportok |500 |3000 |
| Biztonsági csoportok / IP-konfiguráció, egy hálózati adapter |10 |20 |
| IP-konfigurációk alkalmazás biztonsági csoportonként |1000 |4000 |
| Biztonsági csoportok belül a hálózati biztonsági csoport összes biztonsági szabály adható meg |50 |100 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |100 |400 |
| Nyilvános IP-címek – dinamikus |(Basic) 60 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |(Basic) 20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |(Standard) 20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Pont–hely kapcsolati főtanúsítványok száma VPN Gateway-példányonként |20 |20 |

** A alapértelmezett korlátozások vonatkoznak-előfizetések, amelyek korábban nem volt a működés felső korlátjának támogatási növelése

#### <a name="load-balancer"></a>Korlátozza a terheléselosztó
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Megtudhatja, hogyan [megtekintése az előfizetés-korlátozások az aktuális erőforrás-használat](../articles/networking/check-usage-against-limits.md)

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Terheléselosztók | 100 | 1000 |
| Basic erőforrásonként szabályok | 150 | 250 |
| Standard erőforrásonként szabályok | 1250 | 1500 |
| IP-konfiguráció / szabályok | 299 |299 |
| Előtérbeli IP-konfigurációk, Basic | 10 | 200 |
| Előtérbeli IP-konfigurációk, a Standard | 10 | 600 |
| A háttérkészlet, Basic | 100, egy rendelkezésre állási csoport | 100, egy rendelkezésre állási csoport |
| Háttérkészlet, a Standard | 1000, egyetlen virtuális hálózat | 1000, egyetlen virtuális hálózat |
| Terheléselosztó Standard / háttér erőforrások &ast; | 50 | 150 |
| Magas rendelkezésre ÁLLÁSÚ portok, a Standard | belső előtér / 1 | belső előtér / 1 |

&ast; Legfeljebb 150 erőforrások, az önálló virtuális gépek, a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok tetszőleges kombinációját.

Ha meg szeretné növelni a korlátozások alapértelmezett értékeit, [lépjen kapcsolatba az ügyfélszolgálattal](../articles/azure-supportability/resource-manager-core-quotas-request.md ).


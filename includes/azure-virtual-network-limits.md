---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 3e66bf61caf786473d89eab9a6567bb05aff0d19
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457375"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Hálózatkezelési korlátok – Azure Resource Manager az alábbi korlátozások érvényesek, csak a hálózati erőforrások kezelhető **Azure Resource Manager** régiónként és előfizetésenként. Ismerje meg, hogyan [megtekintheti az aktuális erőforrás-használat elleni az előfizetési korlátok](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> A Microsoft a közelmúltban növelte az összes alapértelmezett korlátok a maximális korlátig való. Ha egyetlen oszlop sincs maximális korlátot, akkor az erőforrás nem állítható korlátokkal rendelkeznek. Ha növelni a múltban támogatja ezeket a korlátokat, és nem jelenik meg a frissített korlátait az alábbi táblázatban [nyisson meg egy online támogatási kérést díj nélkül](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | Alapértelmezett/maximális korlát | 
| --- | --- |
| Virtuális hálózatok |1,000 |
| Alhálózatok száma virtuális hálózatonként |3,000 |
| Virtuális hálózati társviszony-létesítések száma virtuális hálózatonként |500 |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |
| Magánhálózati IP-címek száma virtuális hálózatonként |65,536 |
| Magánhálózati IP-címek száma hálózati adapterenként |256 |
| Magánhálózati IP-címek virtuális gépenként |256 |
| Egyidejű TCP vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány adapterenként |500,000 |
| Hálózati kártyák |65,536 |
| Network Security Groups (Hálózati biztonsági csoportok) |5,000 |
| NSG-ben szereplő NSG-szabályok |1,000 |
| IP-címek és a forrás vagy cél egyetlen biztonsági csoportba tartozó megadott tartományok |4,000 |
| Alkalmazásbiztonsági csoportok |3,000 |
| Az alkalmazásbiztonsági csoportok száma / hálózati adapter IP-Címének konfigurálása |20 |
| IP-konfigurációk száma alkalmazásbiztonsági csoport |4,000 |
| Az alkalmazásbiztonsági csoportok belül az összes biztonsági szabályok egy hálózati biztonsági csoport adható meg |100 |
| Felhasználó által megadott útvonaltáblák |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |400 |
| Főtanúsítványok pont – hely Azure VPN Gateway átjárók száma |20 |
| Virtuális hálózat koppint |100 |
| Hálózati adapter KOPPINTSON konfigurációk száma virtuális hálózatonként KOPPINTSON |100 |

#### <a name="publicip-address"></a>Nyilvános IP-cím korlátok
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek – dinamikus | Alapszintű 1000. |Forduljon a támogatási szolgálathoz. |
| Nyilvános IP-címek – statikus | Alapszintű 1000. |Forduljon a támogatási szolgálathoz. |
| Nyilvános IP-címek – statikus | a standard szintű 200.|Forduljon a támogatási szolgálathoz. |
| Nyilvános IP-előtag hossza | /28 | Forduljon a támogatási szolgálathoz. |

#### <a name="load-balancer"></a>Load balancer korlátok
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Ismerje meg, hogyan [megtekintheti az aktuális erőforrás-használat elleni az előfizetési korlátok](../articles/networking/check-usage-against-limits.md).

| Resource | Alapértelmezett/maximális korlát |
| --- | --- |
| Terheléselosztók | 1,000 | 
| Erőforrásonként alapszintű szabályok | 250 |
| Erőforrásonként Standard szabályok | 1,500 | 
| Szabályok száma IP-konfiguráció | 299 |
| Szabályok száma hálózati adapter | 300 |
| Előtérbeli IP-konfigurációk, alapszintű | 200 |
| Előtérbeli IP-konfigurációk, Standard | 600 |
| Háttérkészlet-, Basic | 100, egyetlen rendelkezésre állási csoportban |
| Háttérkészlet-, Standard | 1000, egyetlen virtuális hálózaton |
| Háttér-erőforrások száma terheléselosztónként, Standard<sup>1</sup> | 150 |
| Magas rendelkezésre állású portok, a Standard | 1 / belső előtér |

<sup>1</sup>legfeljebb 150 erőforrásokat, az önálló virtuálisgép-erőforrások tetszőleges kombinációjának határérték, erőforrások és a virtuálisgép-méretezési csoport erőforrások a rendelkezésre állási csoportban.

#### <a name="virtual-networking-limits-classic"></a>Az alábbi korlátozások érvényesek, csak a hálózati erőforrások kezelhető a **klasszikus** előfizetésenként üzemi modellel. Ismerje meg, hogyan [megtekintheti az aktuális erőforrás-használat elleni az előfizetési korlátok](../articles/networking/check-usage-against-limits.md).

| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |100 |100 |
| Helyi hálózati helyek |20 |Forduljon a támogatási szolgálathoz. |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |20 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4,096 |4,096 |
| Egyidejű TCP vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány adapterenként |500 000, legfeljebb 1 000 000 két vagy több hálózati adapter esetében. |500 000, legfeljebb 1 000 000 két vagy több hálózati adapter esetében. |
| Hálózati biztonsági csoportok (NSG-k) |200 |200 |
| NSG-ben szereplő NSG-szabályok |1,000 |1,000 |
| Felhasználó által megadott útvonaltáblák |200 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |400 |400 |
| Nyilvános IP-címek (dinamikus) |500 |500 |
| Fenntartott nyilvános IP-címek |500 |500 |
| Nyilvános virtuális IP-címek száma üzemelő példányonként |5 |Forduljon a támogatási szolgálathoz. |
| Privát VIP (belső terheléselosztás) száma üzemelő példányonként |1 |1 |
| Végponthozzáférés-vezérlési listák (ACL) |50 |50 |

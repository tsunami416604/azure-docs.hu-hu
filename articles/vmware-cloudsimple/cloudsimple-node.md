---
title: VMware megoldást, CloudSimple – Azure csomópontok áttekintése
description: Ismerje meg CloudSimple csomópontok és a fogalmakkal.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165799"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple csomópontok áttekintése

Csomópontnak számít:

* Egy dedikált operációs rendszer nélküli gazdagépet, amelyen telepítve van a VMware ESXi-hipervizorra compute  
* Magánfelhők létrehozása helyezheti üzembe a számítási vagy fenntartott egység  
* Rendelkezésre álló, üzembe helyezése, és foglaljon le egy régióban, ahol a CloudSimple szolgáltatás érhető el.

Csomópontok magánfelhő építőelemei.  Magánfelhő létrehozása a ugyanazon termékváltozat három csomóponttal legalább kell.  Bontsa ki a magánfelhő, adjon hozzá további csomópontokat.  Meglévő fürt csomópontokat adhat hozzá. Vagy létrehozhat egy új fürt üzembe helyezésével csomópontok az Azure Portalon, és társítja őket a CloudSimple szolgáltatással.  Üzembe helyezett összes csomópontja már a CloudSimple szolgáltatás alatt jelennek meg.  Magánfelhő CloudSimple Portal kiépített csomópontjából hozzon létre.

## <a name="provisioned-nodes"></a>Üzembe helyezett csomópontok

Üzembe helyezett csomópontok használatalapú-kapacitást biztosítanak. Csomópontok kiépítése segítségével igény szerint a VMware-fürtök gyors skálázásának. Csomópontok hozzáadása, igény szerint, vagy egy üzembe helyezett csomópont használatával csökkentheti a VMware-fürt törlése. üzembe helyezett csomópontok számlázása havi rendszerességgel történik, és azok üzemelnek, az előfizetésen felüli:

* Ha hitelkártyával fizet az Azure-előfizetése, a kártya azonnal történik.
* A számlázás a számla ellenében, a költségek jelenik meg a következő számla.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware megoldást CloudSimple csomópontok Termékváltozat

A következő típusú csomópontokat kiépítés vagy foglalást érhetők el.

| SKU | CS28 - csomópont | CS36 - csomópont |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz-es, a 28 mag (56 HT) | 2x2.3 GHz-es, 36 mag (72 HT) |
| RAM | 256 GB | 512 GB |
| Gyorsítótárlemez |  1.6-os TB-os NVMe | 3.2-es TB-os NVMe |
| A kapacitás lemez | 5.625 TB nyers | 11,25 TB nyers |
| Tárolás típusa | Az összes Flash | Az összes Flash |

## <a name="limits"></a>Limits

Az alábbi csomópont korlátok vonatkoznak a magánfelhőben.

| Resource | Korlát |
|----------|-------|
| Magánfelhő létrehozása a csomópontok minimális száma | 3 |
| A magánfelhő a fürtben található csomópontok maximális száma | 16 |
| A magánfelhőben csomópontok maximális száma | 64 |
| Új fürt a csomópontok minimális száma | 3 |

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [csomópontok üzembe helyezése](create-nodes.md)
* Ismerje meg [privát felhő](cloudsimple-private-cloud.md)
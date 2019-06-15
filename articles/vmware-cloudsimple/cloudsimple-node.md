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
ms.openlocfilehash: b3c8fca1dd93f379860cc3b084fbb14d4a0c6380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577360"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple csomópontok áttekintése

Csomópontnak számít:

* Egy dedikált operációs rendszer nélküli gazdagépet, amelyen telepítve van a VMware ESXi-hipervizorra compute  
* Magánfelhők létrehozása számítástechnika vásárolhatja meg vagy fenntartott egység  
* Rendelkezésre álló vásárolhatja meg vagy foglaljon le egy régióban, ahol a CloudSimple szolgáltatás érhető el.

Csomópontok magánfelhő építőelemei.  Magánfelhő létrehozása a ugyanazon termékváltozat három csomóponttal legalább kell.  Bontsa ki a magánfelhő, adjon hozzá további csomópontokat.  Meglévő fürt csomópontokat adhat hozzá. Másik lehetőségként a csomópontok az Azure Portalon vásárol, és társítja őket a CloudSimple szolgáltatással létrehozhat egy új fürtöt.  Vásárolt összes csomópontot már a CloudSimple szolgáltatás alatt jelennek meg.  Magánfelhő CloudSimple Portal megvásárolt csomópontjából hozzon létre.

## <a name="purchased-nodes"></a>Megvásárolt csomópontok

Megvásárolt csomópontok használatalapú-kapacitást biztosítanak. Csomópontok megvásárlása segítségével igény szerint a VMware-fürtök gyors skálázásának. Csomópontok hozzáadása, igény szerint, vagy a megvásárolt csomópont használatával csökkentheti a VMware-fürt törlése. Megvásárolt csomópontok számlázása havi rendszerességgel történik, és ha már vásárolt az előfizetésen felüli:

* Ha hitelkártyával fizet az Azure-előfizetése, a kártya azonnal történik.
* A számlázás a számla ellenében, a költségek jelenik meg a következő számla.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware megoldást CloudSimple csomópontok Termékváltozat

A következő típusú csomópontokat beszerzési vagy foglalás érhetők el.

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

* Ismerje meg, hogyan [csomópontok megvásárlása](create-nodes.md)
* Ismerje meg [privát felhő](cloudsimple-private-cloud.md)
---
title: Azure VMware-megoldások (AVS) – a csomópontok áttekintése
description: Ismerkedjen meg az AVS-csomópontokkal és-fogalmakkal.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024925"
---
# <a name="avs-nodes-overview"></a>Az AVS-csomópontok áttekintése

A csomópontok egy AVS privát felhő építőelemei. Csomópont:

* Dedikált operációs rendszer nélküli számítási gazdagép, amelyen telepítve van egy VMware ESXi hypervisor  
* Az AVS privát felhők létrehozásához megvásárolt vagy fenntartott számítástechnikai egység
* Megvásárolható vagy foglalható egy olyan régióban, ahol az AVS szolgáltatás elérhető

Hozzon létre egy AVS Private-felhőt a megvásárolt csomópontokból. Az AVS privát felhő létrehozásához legalább három csomópontra van szükség ugyanahhoz az SKU-hoz. Az AVS Private Cloud kibontásához további csomópontokat adjon hozzá. Csomópontokat adhat hozzá egy meglévő fürthöz, vagy létrehozhat egy új fürtöt, ha megvásárolja a csomópontokat a Azure Portalban, és társítja őket az AVS szolgáltatáshoz. A megvásárolt csomópontok az AVS szolgáltatás alatt láthatók. 

## <a name="provisioned-nodes"></a>Kiépített csomópontok

A kiépített csomópontok utólagos elszámolású kapacitást biztosítanak. A kiépítési csomópontok segítségével gyorsan méretezheti igény szerint a VMware-fürtöt. Szükség szerint csomópontokat adhat hozzá, vagy törölhet egy kiépített csomópontot a VMware-fürt méretezéséhez. A kiépített csomópontok számlázása havonta történik, és a kiépített előfizetéshez kell fizetni.

* Ha hitelkártyával fizet az Azure-előfizetésért, a kártyát azonnal kiszámlázzák.
* Ha számlán számlázunk, a díjak a következő számlán jelennek meg.

## <a name="vmware-solution-by-avs-nodes-sku"></a>VMware-megoldás AVS-csomópontok SKU-ban

A következő típusú csomópontok érhetők el a kiépítés vagy a foglalás számára.

| SKU (Cikkszám)           | CS28 – csomópont                 | CS36 – csomópont                 | CS36m – csomópont                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region (Régió)        | USA keleti régiója, USA nyugati régiója            | USA keleti régiója, USA nyugati régiója            | Nyugat-Európa                 |
| CPU           | 2x 2.2 GHz, 28 mag (56 HT) | 2x 2.3 GHz, 36 mag (72 HT) | 2x 2.3 GHz, 36 mag (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Gyorsítótárlemez    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Kapacitás lemez | 5,625 TB nyers                | 11,25 TB nyers                | 15,36 TB nyers                |
| Tárolási típus  | Minden Flash                   | Minden Flash                   | Minden Flash                   |

## <a name="limits"></a>Korlátozások

A következő csomópontokra vonatkozó korlátozások érvényesek az AVS privát felhőkre.

| Erőforrás | Korlát |
|----------|-------|
| A csomópontok minimális száma egy AVS Private-felhő létrehozásához | 3 |
| Fürtben lévő csomópontok maximális száma egy AVS Private-felhőben | 16 |
| Csomópontok maximális száma egy AVS Private-felhőben | 64 |
| Csomópontok minimális száma egy új fürtön | 3 |

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [vásárolhat csomópontokat](create-nodes.md)
* További információ az [AVS Private felhőkről](cloudsimple-private-cloud.md)
* További információ a [csomópontok](create-nodes.md) kiépítéséről
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)

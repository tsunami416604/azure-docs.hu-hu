---
title: Azure VMware-megoldás CloudSimple – a csomópontok áttekintése
description: Ismerje meg a CloudSimple-csomópontokat és-fogalmakat.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 58af69b401400c7b2f663a91de8bf38bc9a296a3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877703"
---
# <a name="cloudsimple-nodes-overview"></a>A CloudSimple-csomópontok áttekintése

A csomópontok a privát felhő építőelemei. Csomópont:

* Dedikált operációs rendszer nélküli számítási gazdagép, amelyen telepítve van egy VMware ESXi hypervisor  
* Saját felhők létrehozásához megvásárolt vagy fenntartott számítástechnikai egység
* Megvásárolható vagy foglalható egy olyan régióban, ahol a CloudSimple szolgáltatás elérhető

Hozzon létre egy privát felhőt a megvásárolt csomópontokból. Privát felhő létrehozásához legalább három csomópontra van szükség ugyanahhoz az SKU-hoz. Privát felhő kibontásához adjon hozzá további csomópontokat.  Csomópontokat adhat hozzá egy meglévő fürthöz, vagy létrehozhat egy új fürtöt, ha megvásárolja a csomópontokat a Azure Portalban, és társítja őket a CloudSimple szolgáltatáshoz.  Az összes megvásárolt csomópont látható a CloudSimple szolgáltatás alatt.  

## <a name="purchased-nodes"></a>Megvásárolt csomópontok

A megvásárolt csomópontok utólagos elszámolású kapacitást biztosítanak. A csomópontok megvásárlásával gyorsan méretezheti igény szerint a VMware-fürtöt. Szükség szerint csomópontokat adhat hozzá, vagy törölhet egy megvásárolt csomópontot a VMware-fürt méretezéséhez. A megvásárolt csomópontok számlázása havonta történik, és a megvásárolt előfizetés után kell fizetni.

* Ha hitelkártyával fizet az Azure-előfizetésért, a kártyát azonnal kiszámlázzák.
* Ha számlán számlázunk, a díjak a következő számlán jelennek meg.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-megoldás CloudSimple-csomópontok SKU-ban

A következő típusú csomópontok vásárolhatók meg vagy foglalhatók le.

| SKU | CS28 – csomópont | CS36 – csomópont |
|-----|-------------|-------------|
| CPU | 2x 2.2 GHz, 28 mag (56 HT) | 2x 2.3 GHz, 36 mag (72 HT) |
| RAM | 256 GB | 512 GB |
| Lemez gyorsítótára |  1,6 – TB NVMe | 3,2 – TB NVMe |
| Kapacitás lemez | 5,625 TB nyers | 11,25 TB nyers |
| Tárolási típus | Minden Flash | Minden Flash |

## <a name="limits"></a>Korlátok

A következő csomópont-korlátok a privát felhőkre vonatkoznak.

| Resource | Korlát |
|----------|-------|
| A privát felhő létrehozásához szükséges csomópontok minimális száma | 3 |
| Fürtben lévő csomópontok maximális száma egy privát felhőben | 16 |
| A privát felhőben lévő csomópontok maximális száma | 64 |
| Csomópontok minimális száma egy új fürtön | 3 |

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [vásárolhat csomópontokat](create-nodes.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)

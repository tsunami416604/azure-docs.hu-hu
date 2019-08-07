---
title: A VMware-megoldás csomópontjainak áttekintése CloudSimple szerint – Azure
description: Ismerje meg a CloudSimple-csomópontokat és-fogalmakat.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 346bd046810ebae5142bc23400419857000d0c8e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812569"
---
# <a name="cloudsimple-nodes-overview"></a>A CloudSimple-csomópontok áttekintése

Csomópont:

* Dedikált operációs rendszer nélküli számítási gazdagép, ahol a VMware ESXi hypervisor telepítve van  
* Saját felhők létrehozásához vagy lefoglalásához használható számítási egység  
* Elérhető egy olyan régióban való üzembe helyezéshez vagy fenntartáshoz, ahol a CloudSimple szolgáltatás elérhető

A csomópontok egy privát felhő építőelemei.  Privát felhő létrehozásához legalább három csomópontra van szükség ugyanahhoz az SKU-hoz.  Privát felhő kibontásához adjon hozzá további csomópontokat.  Csomópontokat adhat hozzá egy meglévő fürthöz. Másik lehetőségként létrehozhat egy új fürtöt a Azure Portal csomópontjainak kiépítés, és társíthatja azokat a CloudSimple szolgáltatáshoz.  Az összes kiépített csomópont látható a CloudSimple szolgáltatás alatt.  A CloudSimple-portál kiépített csomópontjaiból hozzon létre egy privát felhőt.

## <a name="provisioned-nodes"></a>Kiépített csomópontok

A kiépített csomópontok utólagos elszámolású kapacitást biztosítanak. A kiépítési csomópontok segítségével gyorsan méretezheti igény szerint a VMware-fürtöt. Szükség szerint csomópontokat adhat hozzá, vagy törölhet egy kiépített csomópontot a VMware-fürt méretezéséhez. a kiépített csomópontok havi rendszerességgel kerülnek kiszámlázásra, és a kiépített előfizetésért kell fizetni:

* Ha hitelkártyával fizet az Azure-előfizetésért, a kártyát azonnal kiszámlázzák.
* Ha számlán számlázunk, a díjak a következő számlán jelennek meg.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-megoldás CloudSimple-csomópontok SKU-ban

A következő típusú csomópontok érhetők el a kiépítés vagy a foglalás számára.

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

* További információ a [csomópontok](create-nodes.md) kiépítéséről
* A [Private Cloud](cloudsimple-private-cloud.md) megismerése
---
title: Azure VMware-megoldás a CloudSimple által – Csomópontok áttekintése
description: Ismerje meg a CloudSimple csomópontok és fogalmak.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024925"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple-csomópontok – áttekintés

A csomópontok a magánfelhő építőkövei. A csomópont a következő:

* Egy dedikált, csupasz fém számítási állomás, ahol egy VMware ESXi hipervizor van telepítve  
* A saját felhők létrehozásához rendelkezésre álló vagy lefoglalható számítástechnikai egység
* Olyan régióban érhető el vagy foglalhatja le, ahol a CloudSimple szolgáltatás elérhető

Hozzon létre egy privát felhő a kiépített csomópontokból. Privát felhő létrehozásához legalább három csomópontra van szükség ugyanannak a termékváltozatnak. A magánfelhő bővítéséhez adjon hozzá további csomópontokat.  Csomópontok hozzáadása egy meglévő fürthöz, vagy hozzon létre egy új fürtet az Azure Portalon csomópontok kiépítésével, és társítja őket a CloudSimple szolgáltatás.  Az összes kiépített csomópont látható a CloudSimple szolgáltatás alatt.  

## <a name="provisioned-nodes"></a>Kiépített csomópontok

A kiépített csomópontok felosztó-kiosztó kapacitást biztosítanak. A csomópontok kiépítése segít a VMware-fürt igény szerinti gyors méretezésével. Szükség szerint hozzáadhat csomópontokat, vagy törölhet egy kiépített csomópontot a VMware-fürt leskálázására. A kiépített csomópontok at havonta számlázunk, és az előfizetésre terheljük, ahol ki vannak építve.

* Ha az Azure-előfizetésért hitelkártyával fizet, a kártya számlázása azonnal megtörténik.
* Ha számla alapján számlázunk, a költségek megjelennek a következő számlán.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-megoldás a CloudSimple csomópontjaitól Termékváltozat

A következő típusú csomópontok állnak rendelkezésre a kiépítéshez vagy a foglaláshoz.

| SKU           | CS28 - Csomópont                 | CS36 - Csomópont                 | CS36m - Csomópont                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Régió        | USA keleti része, USA nyugati része            | USA keleti része, USA nyugati része            | Nyugat-Európa                 |
| CPU           | 2x2,2 GHz, 28 mag (56 HT) | 2x2,3 GHz, 36 mag (72 HT) | 2x2,3 GHz, 36 mag (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Gyorsítótárlemez    | 1,6 tb-es NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Kapacitáslemez | 5.625 TB Nyers                | 11.25 TB Nyers                | 15,36 TB Nyers                |
| Tárhelytípusa  | Minden Flash                   | Minden Flash                   | Minden Flash                   |

## <a name="limits"></a>Korlátok

A következő csomópontkorlátok a privát felhőkre vonatkoznak.

| Erőforrás | Korlát |
|----------|-------|
| A magánfelhő létrehozásához szükséges csomópontok minimális száma | 3 |
| A fürt csomópontjainak maximális száma magánfelhőben | 16 |
| A csomópontok maximális száma a magánfelhőben | 64 |
| Az új fürt csomópontjainak minimális száma | 3 |

## <a name="next-steps"></a>További lépések

* További információ a [csomópontok kiépítéséről](create-nodes.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)

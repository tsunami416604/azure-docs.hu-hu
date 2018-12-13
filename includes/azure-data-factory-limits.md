---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 12/12/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 856d702bcf1f566d0cc455cdd2676e6c2e23d179
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53326487"
---
Data factory az olyan több-bérlős szolgáltatás, amely az alábbi alapértelmezett korlátozások, hogy az ügyfél-előfizetések védettek, a többi összes számítási feladatokat a helyen. A korlátok számos egyszerűen kiváltható az előfizetéséhez, a maximális korlátig lépjen kapcsolatba az ügyfélszolgálattal.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Az Azure-előfizetés adat-előállítók | 50 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Adat-előállító entitások (Pipeline, adatkészletek, eseményindítók, társított szolgáltatásokat, integrációs modulok) teljes száma | 5000 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egy előfizetéshez tartozó Azure-SSIS integrációs Runtime(s) teljes Processzormagok | 128 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű folyamatfuttatások száma a data factory (előállító összes folyamatok között megosztott) | 10,000  | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Maximális tevékenységek / folyamat (beleértve a belső tevékenységek tárolók) | 40 | 40 |
| Egy folyamat maximális paraméterek | 50 | 50 |
| ForEach-elemek | 100 000 | 100 000 |
| ForEach-párhuzamosság | 20 | 50 |
| Karakterszám kifejezésenként | 8,192 | 8,192 |
| Átfedésmentes ablakos eseményindító minimális időköz | 15 perc | 15 perc |
| Folyamattevékenység-futtatások maximális időtúllépése | 7 nap | 7 nap |
| Bájt / folyamat objektumok objektum <sup>1</sup> | 200 KB | 200 KB |
| Bájt / szolgáló adatkészlet-objektum és a társított szolgáltatás objektumok <sup>1</sup> | 100 KB | 2000 KB |
| Adatok integrációs egység másolási tevékenység futtatási <sup>3</sup> | 256 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| API-hívások írási | 2500/óra<br/><br/> Ez korlátozva az Azure Resource Manager által, nem az Azure Data Factoryban. | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Elolvashatják az API-hívások | 12 500/óra<br/><br/> Ez korlátozva az Azure Resource Manager által, nem az Azure Data Factoryban. | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Percenkénti lekérdezések figyelése | 1000 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Entitás CRUD-műveletek száma percenként | 50 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az Azure-előfizetés adat-előállítók |50 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Belül egy adat-előállító folyamatok |2500 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Adat-előállító adatkészletek |5000 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Adatkészletenként egyidejű szeletek |10 |10 |
| Bájt / folyamat objektumok objektum <sup>1</sup> |200 KB |200 KB |
| Bájt / szolgáló adatkészlet-objektum és a társított szolgáltatás objektumok <sup>1</sup> |100 KB |2000 KB |
| HDInsight igény szerinti fürt magok egy előfizetésen belül <sup>2</sup> |60 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| A felhőbeli adatáthelyezési egységek száma a másolási tevékenység futtatási <sup>3</sup> |32 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Ismételje meg a folyamattevékenység-futtatások száma |1000 |MaxInt (32 bites) |

<sup>1</sup> folyamat, adatkészlet és a társított szolgáltatás objektumok jelölik az alkalmazások és szolgáltatások logikai csoportosítása. Ezek az objektumok korlátai nem kapcsolódnak, áthelyezése és feldolgozása az Azure Data Factory szolgáltatással adatok mennyisége. A Data factory méretezhető, több petabájtnyi adat kezelésére szolgál.

<sup>2</sup> igény szerinti HDInsight-magok ki az előfizetést, amely tartalmazza az adat-előállító vannak lefoglalva. Ennek eredményeképpen a fenti határértéke az adat-előállító kényszerített magkorlátja igény szerinti HDInsight-magok, és eltér az Azure-előfizetéséhez társított magos korlát.

<sup>3</sup> adatok integrációs egység (DIU) a v2-ben vagy a Felhőbeli adatok adatátviteli egység (DMU) a 1-es használja a cloud-a-felhőbe a másolási műveletek. A Data Factory egy egységet hatékonyságát (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációjával) mérték. Másolás átviteli teljesítménye bizonyos esetekben több DMUs használatával érheti el. Tekintse meg [integrációs adategységek (V2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) és [Felhőbeli adatáthelyezési egységek (V1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) szakaszban talál, és [díjszabását ismertető oldalt az Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) számlázási utalás.

<sup>4</sup> az Integration Runtime (IR) a következő adatintegrációs képességeket biztosít a különböző hálózati környezetekben az Azure Data Factory által használt számítási infrastruktúra: adatáthelyezési zahájeno számítási szolgáltatások, a tevékenységek az SSIS-csomagok végrehajtása. További információkért lásd: [integrációs modul](../articles/data-factory/concepts-integration-runtime.md).

| **Erőforrás** | **Alapértelmezett alacsonyabb korlátot** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Újrapróbálkozási kísérletek közötti időköz |1 másodperc |1 másodperc |
| Ismételje meg az időtúllépés értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webes szolgáltatás hívása korlátok
Az Azure Resource Manager API-hívások korlátokkal rendelkeznek. API-hívások belül válthatnak teheti a [Azure Resource Manager API-korlátok](../articles/azure-subscription-service-limits.md#resource-group-limits).

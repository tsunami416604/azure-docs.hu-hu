---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 05/16/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 63653795ad8c52e2743fb02fa804dd2edbf0d2ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371320"
---
Adat-előállító egy több-bérlős szolgáltatást a következő alapértelmezett korlátokat érdekében győződjön meg arról, hogy az ügyfél-előfizetések védettek, a másik fél munkaterhelések érvényben. A korlátok számos könnyen emelhető az előfizetéséhez, a maximális határértéket akár lépjen kapcsolatba az ügyfélszolgálattal.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát | 
| -------- | ------------- | ------------- | 
| az Azure-előfizetés adat-előállítók | 50 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| belül egy adat-előállító adatcsatornák | 2500 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| egy adat-előállító belül adatkészletek | 2500 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egy adat-előállító belül eseményindítók | 2500 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egy adat-előállító belül összekapcsolt szolgáltatások | 2500 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egy adat-előállító belül integrációs futtatókörnyezetek <sup>4</sup> | 2500 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egy előfizetéshez tartozó teljes CPU-magokat az Azure-SSIS-integráció Runtime(s) | 100 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Folyamat fut egyidejűleg folyamat | 100 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Feldolgozási sor / maximális tevékenységek | 20 | 40 |
| Feldolgozási sor / maximális paraméterek | 20 | 30 |
| Adatcsatorna objektumok objektumonként bájt <sup>1</sup> | 200 KB | 200 KB |
| Bájt / adatkészlet-objektumot, és a társított szolgáltatás objektumok <sup>1</sup> | 100 KB | 2000 KB |
| Adatok adatátviteli egység / tevékenységfuttatási felhőalapú <sup>3</sup> | 256 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Újbóli próbálkozások száma az adatcsatorna tevékenység fut | 1 day(timeout) | 1 nap (timeout) |
| Az API-hívásokban írása | 2500/hr<br/><br/> Ez korlátozva által Azure erőforrás-kezelő nem az Azure Data Factory. | [Forduljon a támogatási szolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Olvassa el az API-hívásokban | 12 500/hr<br/><br/> Ez korlátozva által Azure erőforrás-kezelő nem az Azure Data Factory. | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| az Azure-előfizetés adat-előállítók |50 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| belül egy adat-előállító adatcsatornák |2500 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| egy adat-előállító belül adatkészletek |5000 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| adatkészlet száma párhuzamos szeletek |10 |10 |
| Adatcsatorna objektumok objektumonként bájt <sup>1</sup> |200 KB |200 KB |
| Bájt / adatkészlet-objektumot, és a társított szolgáltatás objektumok <sup>1</sup> |100 KB |2000 KB |
| A HDInsight fürt igény szerinti magok előfizetésen belül <sup>2</sup> |60 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Adatok adatátviteli egység / tevékenységfuttatási felhőalapú <sup>3</sup> |32 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Újbóli próbálkozások száma az adatcsatorna tevékenység fut |1000 |MaxInt (32 bites) |

<sup>1</sup> folyamat, a DataSet adatkészlet és a társított szolgáltatás objektumok határoz meg a számítási feladatok logikai csoportosítása. Ezek az objektumok korlátairól áthelyezni, és az Azure Data Factory szolgáltatással feldolgozni adatok mennyisége nem kapcsolódnak. Adat-előállító méretezési adatmennyiségig kezelésére szolgál.

<sup>2</sup> igény szerinti HDInsight magok kívül az előfizetést, amely tartalmazza az adat-előállítóban foglal le. Ennek eredményeképpen a fenti határértéke a Data Factory igény szerinti HDInsight magok core korlát érvényes, és eltér a fő korlát az Azure-előfizetéshez társított.

<sup>3</sup> felhő adatok adatátviteli egység (DMU) használja a felhő-felhő másolási műveletek. Egy mérték, amely jelöli az (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációja) adat-előállítóban egyetlen egységben. Nagyobb másolási átviteli teljesítményt érhet el, további DMUs használó egyes forgatókönyvek esetén. Tekintse meg [adatok adatátviteli egység (V2) a felhő](../articles/data-factory/copy-activity-performance.md#cloud-data-movement-units) és [adatok adatátviteli egység (V1) a felhő](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) részletei szakaszban.

<sup>4</sup> az integrációs futásidejű (IR) a számítási infrastruktúrát a következő adatok integrációs képességeket biztosítják a különböző hálózati környezetek között Azure Data Factory használatával: adatátvitelt jelölik a tevékenységeket érdemes olyan szolgáltatások, számítási terjesztéséhez SSIS-csomagok végrehajtását. További információkért lásd: [integrációs futásidejű áttekintése](../articles/data-factory/concepts-integration-runtime.md).

| **Erőforrás** | **Alapértelmezett alacsonyabb korlátot** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Újrapróbálkozási kísérletek közötti időközt |1 másodperc |1 másodperc |
| Ismételje meg az időtúllépés értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webes szolgáltatás hívása korlátok
Az Azure Resource Manager API-hívások korlátairól rendelkezik. Az API-hívásokban belül sebességgel végezheti el a [Azure Resource Manager API korlátozza](../articles/azure-subscription-service-limits.md#resource-group-limits).

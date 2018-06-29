---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/20/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 43408ebc65d4acf581b612e8ecfb9d00679cc078
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066101"
---
Adat-előállító egy több-bérlős szolgáltatást a következő alapértelmezett korlátokat érdekében győződjön meg arról, hogy az ügyfél-előfizetések védettek, a másik fél munkaterhelések érvényben. A korlátok számos könnyen emelhető az előfizetéséhez, a maximális határértéket akár lépjen kapcsolatba az ügyfélszolgálattal.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Az Azure-előfizetés adat-előállítók | 50 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Entitások (adatcsatorna, adatkészleteket, eseményindítók, társított szolgáltatások, integrációs futtatókörnyezetek) belül egy adat-előállító teljes száma | 5000 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egy előfizetéshez tartozó teljes CPU-magokat az Azure-SSIS-integráció Runtime(s) | 100 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Folyamat fut egyidejűleg folyamat | 100 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Adat-előállító egyidejű folyamat fut | 10,000  | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Maximális tevékenységek száma (tartalmazza a tárolók belső tevékenységek) folyamat | 40 | 40 |
| Feldolgozási sor / maximális paraméterek | 50 | 50 |
| ForEach-elemek | 100,000 | 100,000 |
| ForEach párhuzamossági | 20 | 50 |
| Karakteres kifejezés | 8,192 | 8,192 |
| Minimális Átfedésmentes ablak eseményindító időköz | 15 perc | 15 perc |
| Időtúllépés maximális csővezeték tevékenység fut | 7 nap | 7 nap |
| Adatcsatorna objektumok objektumonként bájt <sup>1</sup> | 200 KB | 200 KB |
| Bájt / adatkészlet-objektumot, és a társított szolgáltatás objektumok <sup>1</sup> | 100 KB | 2000 KB |
| Adatok integrációs egység másolási tevékenység során futtassa <sup>3</sup> | 256 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Az API-hívásokban írása | 2500/hr<br/><br/> Ez korlátozva által Azure erőforrás-kezelő nem az Azure Data Factory. | [Forduljon a támogatási szolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Olvassa el az API-hívásokban | 12 500/hr<br/><br/> Ez korlátozva által Azure erőforrás-kezelő nem az Azure Data Factory. | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az Azure-előfizetés adat-előállítók |50 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Belül egy adat-előállító adatcsatornák |2500 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egy adat-előállító belül adatkészletek |5000 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Adatkészlet száma párhuzamos szeletek |10 |10 |
| Adatcsatorna objektumok objektumonként bájt <sup>1</sup> |200 KB |200 KB |
| Bájt / adatkészlet-objektumot, és a társított szolgáltatás objektumok <sup>1</sup> |100 KB |2000 KB |
| A HDInsight fürt igény szerinti magok előfizetésen belül <sup>2</sup> |60 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| A felhő adatok adatátviteli egység másolási tevékenység során futtassa <sup>3</sup> |32 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Újbóli próbálkozások száma az adatcsatorna tevékenység fut |1000 |MaxInt (32 bites) |

<sup>1</sup> folyamat, a DataSet adatkészlet és a társított szolgáltatás objektumok határoz meg a számítási feladatok logikai csoportosítása. Ezek az objektumok korlátairól áthelyezni, és az Azure Data Factory szolgáltatással feldolgozni adatok mennyisége nem kapcsolódnak. Adat-előállító méretezési adatmennyiségig kezelésére szolgál.

<sup>2</sup> igény szerinti HDInsight magok kívül az előfizetést, amely tartalmazza az adat-előállítóban foglal le. Ennek eredményeképpen a fenti határértéke a Data Factory igény szerinti HDInsight magok core korlát érvényes, és eltér a fő korlát az Azure-előfizetéshez társított.

<sup>3</sup> adatok integrációs egység (DIU) 2-es verzió vagy felhő adatok adatátviteli egység (DMU) a 1-es használja a felhő-felhőbe történő másolás művelet. Egy mérték, amely jelöli az (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációja) adat-előállítóban egyetlen egységben. Nagyobb másolási átviteli teljesítményt érhet el, további DMUs használó egyes forgatókönyvek esetén. Tekintse meg [adategységek integrációs (V2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) és [adatok adatátviteli egység (V1) a felhő](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) szakaszt, részleteket és [Azure Data Factory árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/data-factory/) számlázási Ennek következménye.

<sup>4</sup> az integrációs futásidejű (IR) a számítási infrastruktúrát a következő adatok integrációs képességeket biztosítják a különböző hálózati környezetek között Azure Data Factory használatával: adatátvitelt jelölik a tevékenységeket érdemes olyan szolgáltatások, számítási terjesztéséhez SSIS-csomagok végrehajtását. További információkért lásd: [integrációs futásidejű áttekintése](../articles/data-factory/concepts-integration-runtime.md).

| **Erőforrás** | **Alapértelmezett alacsonyabb korlátot** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Újrapróbálkozási kísérletek közötti időközt |1 másodperc |1 másodperc |
| Ismételje meg az időtúllépés értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webes szolgáltatás hívása korlátok
Az Azure Resource Manager API-hívások korlátairól rendelkezik. Az API-hívásokban belül sebességgel végezheti el a [Azure Resource Manager API korlátozza](../articles/azure-subscription-service-limits.md#resource-group-limits).

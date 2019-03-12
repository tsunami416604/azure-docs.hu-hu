---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 42c1856f30484532e1ace2e84187bcaaacdf4c72
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553516"
---
Az Azure Data Factory egy több-bérlős szolgáltatás, amely a következő alapértelmezett korlátokkal rendelkeznek, hogy az ügyfél-előfizetések védettek, a többi összes számítási feladatokat a helyen. A korlátok a maximális az előfizetéshez tartozó növeléséhez forduljon az ügyfélszolgálathoz.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Az Azure-előfizetés adat-előállítók | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entitások, például folyamatokat, adatkészletek, eseményindítók, társított szolgáltatásokat és integrációs modulok, adat-előállító belül teljes száma | 5000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy előfizetéshez tartozó Azure-SSIS integrációs modulok esetében teljes Processzormagok | 256 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű folyamatfuttatások száma, amelyet használ az összes folyamatok előállító a data factory | 10,000  | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy folyamatot, amely tartalmazza a tárolók a belső tevékenységek maximális tevékenységek | 40 | 40 |
| Hozható létre a saját üzemeltetésű integrációs ellen társított integrációs modulok maximális száma | 20 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy folyamat maximális paraméterek | 50 | 50 |
| ForEach-elemek | 100 000 | 100 000 |
| ForEach-párhuzamosság | 20 | 50 |
| Karakterszám kifejezésenként | 8,192 | 8,192 |
| Minimális átfedésmentes ablak eseményindító időköz | 15 perc | 15 perc |
| Maximális időtúllépése a folyamatok tevékenységeit futtatja | 7 nap | 7 nap |
| Bájt / folyamat objektumok objektum<sup>1</sup> | 200 KB | 200 KB |
| Bájt / szolgáló adatkészlet-objektum és a társított szolgáltatás objektumok<sup>1</sup> | 100 KB | 2000 KB |
| Adatok integrációs egység másolási tevékenység futtatási<sup>3</sup> | 256 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-hívások írási | 2500/óra<br/><br/> Ez korlátozva az Azure Resource Manager által, nem az Azure Data Factoryban. | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Elolvashatják az API-hívások | 12 500/óra<br/><br/> Ez korlátozva az Azure Resource Manager által, nem az Azure Data Factoryban. | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Percenkénti lekérdezések figyelése | 1,000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entitás CRUD-műveletek száma percenként | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |


### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az Azure-előfizetés adat-előállítók |50 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Belül egy adat-előállító folyamatok |2,500 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adat-előállító belül adatkészletek |5000 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy adatkészlet egyidejű szeletek |10 |10 |
| Bájt / folyamat objektumok objektum<sup>1</sup> |200 KB |200 KB |
| Bájt / objektumra vonatkozó adatokat, és társított szolgáltatási objektumok<sup>1</sup> |100 KB |2000 KB |
| Azure HDInsight-fürt igény szerinti magok egy előfizetésen belül<sup>2</sup> |60 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| A felhőbeli adatáthelyezési egységek száma a másolási tevékenység futtatási<sup>3</sup> |32 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ismételje meg a folyamattevékenység-futtatások száma |1,000 |MaxInt (32 bites) |

<sup>1</sup>folyamat, adatkészlet és a társított szolgáltatás objektumok jelölik az alkalmazások és szolgáltatások logikai csoportosítása. Ezek az objektumok korlátai, áthelyezése és feldolgozása az Azure Data Factoryvel adatok mennyisége nem vonatkoznak. A Data Factory méretezhető, több petabájtnyi adat kezelésére szolgál.

<sup>2</sup>igény szerinti HDInsight-magok ki az előfizetést, amely tartalmazza az adat-előállító vannak lefoglalva. Ennek eredményeképpen a korábbi határértéke a Data Factory által kényszerített magkorlátja igény szerinti HDInsight-magok. Különbözik az Azure-előfizetéséhez társított magos korlát.

<sup>3</sup>integrációs adategység (DIU) 2-es verzióját, vagy a felhőbeli adatáthelyezési egység (DMU) az 1. verziójának egy felhő-a-felhőbe másolási művelet szolgál. Egy mérték, amely a Data Factory egy egységet hatékonyságát. A Processzor, memória és a hálózati erőforrás-hozzárendelések ötvözi. Másolás átviteli teljesítménye bizonyos esetekben több DMUs használatával érheti el. További információkért lásd: [adatok integrációs egység (2. verzió)](../articles/data-factory/copy-activity-performance.md#data-integration-units) és [adatáthelyezési egységek (1-es) Cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Számlázási információkért lásd: [Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>4</sup>az integrációs modul (IR) az adatintegrációs képességeket biztosít a különböző hálózati környezetekben, adatáthelyezés, szállítási tevékenység számítási szolgáltatások, például az Azure Data Factory által használt számítási infrastruktúra és a az SSIS-csomagok végrehajtása. További információkért lásd: [Integration runtime áttekintése](../articles/data-factory/concepts-integration-runtime.md).

| **Erőforrás** | **Alapértelmezett alacsonyabb korlátot** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Újrapróbálkozási kísérletek közötti időköz |1 másodperc |1 másodperc |
| Ismételje meg az időtúllépés értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webes szolgáltatás hívása korlátok
Az Azure Resource Manager API-hívások korlátokkal rendelkeznek. API-hívások belül válthatnak teheti a [Azure Resource Manager API-korlátok](../articles/azure-subscription-service-limits.md#resource-group-limits).

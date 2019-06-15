---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427611"
---
Az Azure Data Factory egy több-bérlős szolgáltatás, amely a következő alapértelmezett korlátokkal rendelkeznek, hogy az ügyfél-előfizetések védettek, a többi összes számítási feladatokat a helyen. A korlátok a maximális az előfizetéshez tartozó növeléséhez forduljon az ügyfélszolgálathoz.

### <a name="version-2"></a>2-es verzió

| Resource | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Az Azure-előfizetés adat-előállítók | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entitások, például folyamatokat, adatkészletek, eseményindítók, társított szolgáltatásokat és integrációs modulok, adat-előállító belül teljes száma | 5,000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy előfizetéshez tartozó Azure-SSIS integrációs modulok esetében teljes Processzormagok | 256 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű folyamatfuttatások száma, amelyet használ az összes folyamatok előállító a data factory | 10,000  | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű külső tevékenységfuttatások előfizetésenként [integrációs modul Azure-régió](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Külső tevékenységeket az integrációs modul felügyelt, de végrehajtani a társított szolgáltatásokat, beleértve a Databricks, a tárolt eljárást, Hdinsight és mások.</small> | 3000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű folyamat tevékenységfuttatások előfizetésenként [integrációs modul Azure-régió](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Adatcsatorna tevékenységei integrációs modulban, beleértve a Lookup, GetMetadata, hajtsa végre, és törölni. </small>| 1000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Szerzői műveletek előfizetésenként egyidejű [integrációs modul Azure-régió](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Például kapcsolat tesztelése, a Tallózás a mappák listája és a tábla tekintse meg adatok. | 200 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű adategységek integrációs<sup>1</sup> előfizetésenként és felhasználási [integrációs modul Azure-régió](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| 1 csoport régió<sup>2</sup>: 6000<br>2\. régió csoport<sup>2</sup>: 3000<br>3\. csoport régió<sup>2</sup>: 1500 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy folyamatot, amely tartalmazza a tárolók a belső tevékenységek maximális tevékenységek | 40 | 40 |
| Hozható létre a saját üzemeltetésű integrációs ellen társított integrációs modulok maximális száma | 100 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy folyamat maximális paraméterek | 50 | 50 |
| ForEach-elemek | 100,000 | 100,000 |
| ForEach-párhuzamosság | 20 | 50 |
| Karakterszám kifejezésenként | 8,192 | 8,192 |
| Minimális átfedésmentes ablak eseményindító időköz | 15 perc | 15 perc |
| Maximális időtúllépése a folyamatok tevékenységeit futtatja | 7 nap | 7 nap |
| Bájt / folyamat objektumok objektum<sup>3</sup> | 200 KB | 200 KB |
| Bájt / szolgáló adatkészlet-objektum és a társított szolgáltatás objektumok<sup>3</sup> | 100 KB | 2000 KB |
| Adategységek integrációs<sup>1</sup> / másolási tevékenység futtatása | 256 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-hívások írási | 2500/óra<br/><br/> Ez korlátozva az Azure Resource Manager által, nem az Azure Data Factoryban. | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Elolvashatják az API-hívások | 12 500/óra<br/><br/> Ez korlátozva az Azure Resource Manager által, nem az Azure Data Factoryban. | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Percenkénti lekérdezések figyelése | 1,000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entitás CRUD-műveletek száma percenként | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> integrációs adategység (DIU) szolgál a felhő-a-felhőbe a másolási műveletek, és ismerje meg alaposabban [adatok integrációs egység (2. verzió)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Számlázási információkért lásd: [Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [azure integrációs modul](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) van [globálisan elérhető](https://azure.microsoft.com/global-infrastructure/services/) adatok megfelelőség biztosítása érdekében hatékonyságát, és csökkentheti a hálózati kimenő forgalom költségeit. 

| Régió-csoport | Régiók | 
| -------- | ------ |
| 1 csoport régió | USA középső RÉGIÓJA, USA keleti RÉGIÓJA, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa, USA nyugati RÉGIÓJA, USA 2. nyugati |
| 2\. csoport régió | Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, közép-India, kelet-japán, Northcentral Egyesült Államok, déli RÉGIÓJA, Délkelet-Ázsia, USA nyugati középső RÉGIÓJA |
| 3\. csoport régió | Canada Central, East Asia, France Central, Korea Central, UK South |

<sup>3</sup> folyamat, adatkészlet és a társított szolgáltatás objektumok jelölik az alkalmazások és szolgáltatások logikai csoportosítása. Ezek az objektumok korlátai, áthelyezése és feldolgozása az Azure Data Factoryvel adatok mennyisége nem vonatkoznak. A Data Factory méretezhető, több petabájtnyi adat kezelésére szolgál.

### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az Azure-előfizetés adat-előállítók |50 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Belül egy adat-előállító folyamatok |2,500 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adat-előállító belül adatkészletek |5,000 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy adatkészlet egyidejű szeletek |10 |10 |
| Bájt / folyamat objektumok objektum<sup>1</sup> |200 KB |200 KB |
| Bájt / objektumra vonatkozó adatokat, és társított szolgáltatási objektumok<sup>1</sup> |100 KB |2000 KB |
| Azure HDInsight-fürt igény szerinti magok egy előfizetésen belül<sup>2</sup> |60 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| A felhőbeli adatáthelyezési egységek száma a másolási tevékenység futtatási<sup>3</sup> |32 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ismételje meg a folyamattevékenység-futtatások száma |1,000 |MaxInt (32 bites) |

<sup>1</sup> folyamat, adatkészlet és a társított szolgáltatás objektumok jelölik az alkalmazások és szolgáltatások logikai csoportosítása. Ezek az objektumok korlátai, áthelyezése és feldolgozása az Azure Data Factoryvel adatok mennyisége nem vonatkoznak. A Data Factory méretezhető, több petabájtnyi adat kezelésére szolgál.

<sup>2</sup> igény szerinti HDInsight-magok ki az előfizetést, amely tartalmazza az adat-előállító vannak lefoglalva. Ennek eredményeképpen a korábbi határértéke a Data Factory által kényszerített magkorlátja igény szerinti HDInsight-magok. Különbözik az Azure-előfizetéséhez társított magos korlát.

<sup>3</sup> a felhőbeli adatáthelyezési egység (DMU) 1. verziójának egy felhő-a-felhőbe másolási művelet használja a rendszer további információkat talál a [adatáthelyezési egységek (1-es) Cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Számlázási információkért lásd: [Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

| **Erőforrás** | **Alapértelmezett alacsonyabb korlátot** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Újrapróbálkozási kísérletek közötti időköz |1 másodperc |1 másodperc |
| Ismételje meg az időtúllépés értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webes szolgáltatás hívása korlátok
Az Azure Resource Manager API-hívások korlátokkal rendelkeznek. API-hívások belül válthatnak teheti a [Azure Resource Manager API-korlátok](../articles/azure-subscription-service-limits.md#resource-group-limits).

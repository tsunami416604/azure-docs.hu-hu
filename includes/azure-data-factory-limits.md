---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086140"
---
Az Azure Data Factory egy több-bérlős szolgáltatás, amely a következő alapértelmezett korlátokkal rendelkezik, hogy biztosítsa az ügyfél-előfizetések védelmét egymás számítási feladataival szemben. Lépjen kapcsolatba az ügyfélszolgálattal, ha növelni szeretné az előfizetésére vonatkozó korlátot, akár a maximumig.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Adat-előállítók az Azure-előfizetésekben | 800 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy adat-előállítón belüli entitások, például folyamatok, adathalmazok, triggerek, társított szolgáltatások és integrációs modulok teljes száma | 5000 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Az Azure-SSIS integrációs modulokhoz tartozó processzormagok teljes száma egy előfizetésen belül | 256 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű folyamatfuttatások adat-előállítónként, az adat-előállító összes folyamatára vonatkozóan | 10,000  | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű külső tevékenységfuttatások előfizetésenként és [Azure-beli integrációsmodul-régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>A külső tevékenységek integrációs modulon vannak kezelve, de összekapcsolt szolgáltatásokon vannak végrehajtva (például: Databricks, tárolt eljárások, HDInsights, webes tevékenységek és egyebek).</small> | 3000 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű folyamattevékenység-futtatások előfizetésenként és [Azure-beli integrációsmodul-régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>A folyamattevékenységek integrációs modulon lesznek végrehajtva, beleértve a keresést, a metaadatok beolvasását és a törlést. </small>| 1000 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű szerzői műveletek előfizetésenként és [Azure-beli integrációsmodul-régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Többek között kapcsolat tesztelése, mappa- és táblázatlista böngészése, valamint adatok előnézete. | 200 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Az Adatintegrációs egységek<sup>1</sup> egyidejű felhasználása előfizetésenként és [Azure-beli integrációsmodul-régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| 1\. régiócsoport <sup>2</sup>: 6000<br>2\. régiócsoport <sup>2</sup>: 3000<br>3\. régiócsoport <sup>2</sup>: 1500 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tevékenységek maximális száma folyamatonként, beleértve a tárolók belső tevékenységeit | 40 | 40 |
| A saját üzemeltetésű integrációs modulonként létrehozható összekapcsolt integrációs modulok maximális száma | 100 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Folyamatonkénti maximális paraméterszám | 50 | 50 |
| ForEach-elemek | 100 000 | 100 000 |
| ForEach-párhuzamosság | 20 | 50 |
| Várólistán várakozó futtatások maximális száma folyamatonként | 100 | 100 |
| Kifejezésenkénti karakterszám | 8,192 | 8,192 |
| Átfedésmentes ablakos trigger minimális intervalluma | 15 perc | 15 perc |
| A folyamattevékenység-futtatások maximális időkorlátja | 7 nap | 7 nap |
| Objektumonkénti bájtok száma folyamatobjektumok esetében<sup>3</sup> | 200 KB | 200 KB |
| Objektumonkénti bájtok száma adathalmaz- és összekapcsoltszolgáltatás-objektumok esetében<sup>3</sup> | 100 KB | 2000 KB |
| Adatintegrációs egységek<sup>1</sup> másolási tevékenységfuttatásonként | 256 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Írási API-hívások | 1200/óra<br/><br/> Ezt a korlátot az Azure Resource Manager, és nem az Azure Data Factory szabja meg. | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Olvasási API-hívások | 12 500/óra<br/><br/> Ezt a korlátot az Azure Resource Manager, és nem az Azure Data Factory szabja meg. | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorozási lekérdezések percenként | 1,000 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entitások CRUD-műveletei percenként | 50 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adatfolyam hibakeresési munkamenetének maximális időtartama | 8 óra | 8 óra |
| Egyidejű adatfolyamok száma előállítónként | 50 | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adatfolyam-hibakeresési munkamenetek egyidejű száma felhasználónként és előállítónként | 3 | 3 |
| Azure integrációs modulok élettartamkorlátja adatfolyamok esetében | 4 óra | [Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Az adatintegrációs egység (DIU) a felhők közötti másolási műveletek során használatos. További információ: [Adatintegrációs egységek (2. verzió)](../articles/data-factory/copy-activity-performance.md#data-integration-units). A díjszabásra vonatkozó információkért tekintse meg az [Az Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/) című szakaszt.

<sup>2</sup> Az [Azure-beli integrációs modul](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) az adatok megfelelősége, a hatékonyság és a hálózat kimeneti forgalmának alacsonyabb költségei érdekében [globálisan elérhető](https://azure.microsoft.com/global-infrastructure/services/). 

| Régiócsoport | Régiók | 
| -------- | ------ |
| 1\. régiócsoport | USA középső régiója, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa, USA nyugati régiója, USA 2. nyugati régiója |
| 2\. régiócsoport | Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-India, Kelet-Japán, USA északi középső régiója, USA déli középső régiója, Délkelet-Ázsia, USA nyugati középső régiója |
| 3\. régiócsoport | Közép-Kanada, Kelet-Ázsia, Franciaország középső régiója, Korea középső régiója, az Egyesült Királyság déli régiója |

<sup>3</sup> A folyamat-, adathalmaz- és összekapcsoltszolgáltatás-objektumok a számítási feladatok logikai csoportosítását képviselik. Ezen objektumok korlátai nincsenek kapcsolatban az Azure Data Factory használatával áthelyezhető és feldolgozható adatok mennyiségével. A Data Factory méretezhető, hogy képes legyen több petabájtnyi adat kezelésére.

### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Maximális korlát** |
| --- | --- | --- |
| Folyamatok száma egy adat-előállítón belül |2500 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adathalmazok száma egy adat-előállítón belül |5000 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű szeletek száma adathalmazonként |10 |10 |
| Objektumonkénti bájtok száma folyamatobjektumok esetében<sup>1</sup> |200 KB |200 KB |
| Objektumonkénti bájtok száma adathalmaz- és összekapcsoltszolgáltatás-objektumok esetében<sup>1</sup> |100 KB |2000 KB |
| Azure HDInsight igény szerinti fürtmagok egy előfizetésen belül<sup>2</sup> |60 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Felhőbeli adatáthelyezési egységek másolási tevékenységfuttatásonként<sup>3</sup> |32 |[Kapcsolatfelvétel az ügyfélszolgálattal](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Folyamattevékenység-futtatások újrapróbálkozásainak száma |1,000 |MaxInt (32 bit) |

<sup>1</sup> A folyamat-, adathalmaz- és összekapcsoltszolgáltatás-objektumok a számítási feladatok logikai csoportosítását képviselik. Ezen objektumok korlátai nincsenek kapcsolatban az Azure Data Factory használatával áthelyezhető és feldolgozható adatok mennyiségével. A Data Factory méretezhető, hogy képes legyen több petabájtnyi adat kezelésére.

<sup>2</sup> Az igény szerinti HDInsight-magok az adat-előállítót tartalmazó előfizetésből lesznek lefoglalva. Ennek következtében az előző korlát a Data Factory által kényszerített magkorlát az igény szerinti HDInsight-magok tekintetében. Ez különbözik az Azure-előfizetéshez tartozó magkorláttól.

<sup>3</sup> Az 1. verzióhoz tartozó felhőbeli adatáthelyezési egység (DMU) a felhők közötti másolási műveletekben használatos. További információ: [Felhőbeli adatáthelyezési egységek (1. verzió)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). A díjszabásra vonatkozó információkért tekintse meg az [Az Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/) című szakaszt.

| **Erőforrás** | **Alapértelmezett alsó korlát** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési intervallum |15 perc |15 perc |
| Az újrapróbálkozási kísérletek közötti intervallum |1 másodperc |1 másodperc |
| Újrapróbálkozási időkorlát értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webszolgáltatások hívási korlátai
Az Azure Resource Manager esetében az API-hívásokra vonatkozó korlátok vannak érvényben. Az API-hívások [Az Azure Resource Manager API-korlátain](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) belüli ütemben kezdeményezhetők.

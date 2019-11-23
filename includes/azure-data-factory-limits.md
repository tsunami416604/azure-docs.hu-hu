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
ms.openlocfilehash: 6dc846ab6af0ed4d6d48c6f6db0abbce06427c36
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035883"
---
Azure Data Factory egy több-bérlős szolgáltatás, amely a következő alapértelmezett korlátokkal rendelkezik, hogy az ügyfél-előfizetések védve legyenek egymás munkaterhelésével szemben. Ha a korlátot az előfizetése maximumán szeretné növelni, forduljon az ügyfélszolgálathoz.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Azure-előfizetéshez tartozó adatfeldolgozók | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egy adat-előállítón belüli entitások (például folyamatok, adathalmazok, eseményindítók, társított szolgáltatások és integrációs modulok) teljes száma | 5000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Azure-SSIS integrációs modulokhoz tartozó összes CPU-mag egy előfizetésben | 256 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| A gyárban található összes folyamat között megosztott, egyidejű adatfeldolgozási folyamat | 10,000  | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű külső tevékenység-előfizetések száma [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>A külső tevékenységek kezelése az integrációs modulon történik, de a társított szolgáltatásokon, például a Databricks, a tárolt eljárásokon, a Hdinsight és másokon végezhető el.</small> | 3000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű folyamat tevékenység-előfizetések száma [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>A folyamat tevékenységei az integrációs modulon futnak, beleértve a keresést, a GetMetadata és a törlést is.</small>| 1000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Előfizetések egyidejű létrehozási műveletei [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>, beleértve a tesztelési kapcsolatokat, a mappák listáját és a táblázatok listáját, az előnézeti adatmegjelenítést. | 200 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű adatintegrációs egységek<sup>1</sup> előfizetések száma [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| 1\. régió<sup>2</sup>. csoport: 6000<br>2\. régió<sup>2</sup>. csoport: 3000<br>3\. régió<sup>2</sup>. csoport: 1500 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tevékenységek maximális száma folyamaton keresztül, amely belső tevékenységeket tartalmaz a tárolók számára | 40 | 40 |
| Egy saját üzemeltetésű integrációs modulon keresztül létrehozható összekapcsolt integrációs modulok maximális száma | 100 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximális paraméterek/folyamat | 50 | 50 |
| ForEach elemek | 100 000 | 100 000 |
| ForEach-párhuzamosság | 20 | 50 |
| Karakter/kifejezés | 8,192 | 8,192 |
| Minimális késleltetésű ablak-trigger időköze | 15 perc | 15 perc |
| A folyamat által futtatott tevékenységek maximális időtúllépése | 7 nap | 7 nap |
| Bájtok száma objektumként a (<sup>3</sup> .) folyamat objektumaihoz | 200 KB | 200 KB |
| Bájt/objektum az adatkészlet és a társított szolgáltatási objektumok esetében<sup>3</sup> | 100 KB | 2 000 KB |
| Adatintegrációs egységek<sup>1</sup> /másolási tevékenységének futtatása | 256 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-hívások írása | 1200/h<br/><br/> Ezt a korlátot Azure Resource Manager, nem Azure Data Factory. | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-hívások olvasása | 12500/h<br/><br/> Ezt a korlátot Azure Resource Manager, nem Azure Data Factory. | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Lekérdezések figyelése percenként | 1,000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entitás-SZIFILISZi műveletek percenként | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Az adatfolyam hibakeresési munkamenetének maximális ideje | 8 óra | 8 óra |
| Adatfolyamatok egyidejű száma egy gyárban | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adatfolyam-hibakeresési munkamenetek egyidejű száma felhasználónként | 3 | 3 |
| Adatfolyam Azure IR ÉLETTARTAMának korlátja | 4 óra | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> az adatintegrációs egység (DIU) felhőből felhőbe másolási műveletben használatos, további információ az [adatintegrációs egységekről (2. verzió)](../articles/data-factory/copy-activity-performance.md#data-integration-units). További információ a számlázásról: [Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) [globálisan elérhető](https://azure.microsoft.com/global-infrastructure/services/) az adatmegfelelőség, a hatékonyság és a csökkentett hálózati kimenő költségek biztosításához. 

| Régió csoport | Regions | 
| -------- | ------ |
| 1\. régió | USA középső régiója, USA keleti régiója, Kelet-RÉGIÓJA, Észak-Európa, Nyugat-Európa, USA nyugati régiója, USA 2. nyugati régiója |
| 2\. régió | Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-India, Kelet-Japán, Northcentral, USA, Southcentral, Délkelet-Ázsia, USA nyugati középső régiója |
| 3\. régió | Közép-Kanada, Kelet-Ázsia, Közép-Németország, Közép-Korea, Egyesült Királyság déli régiója |

<sup>3</sup> a folyamat, az adathalmaz és a társított szolgáltatási objektumok a számítási feladatok logikai csoportosítását jelölik. Ezeknek az objektumoknak a korlátai nem kapcsolódnak a Azure Data Factory használatával áthelyezhető és feldolgozható adatmennyiséghez. Data Factory úgy van kialakítva, hogy az petabájt kezeljen.

### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Azure-előfizetéshez tartozó adatfeldolgozók |50 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adatfeldolgozón belüli folyamatok |2,500 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adatkészletek az adat-előállítón belül |5000 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű szeletek egy adatkészletben |10 |10 |
| Bájt/objektum a folyamat objektumaihoz<sup>1</sup> |200 KB |200 KB |
| Adathalmaz és társított szolgáltatási objektumok bájtjainak száma (bájt/objektum<sup>) 1</sup> |100 KB |2 000 KB |
| Azure HDInsight igény szerinti fürt magok a<sup>2</sup> . előfizetésen belül |60 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Felhőbeli adatáthelyezési egység/másolási tevékenység<sup>3</sup> . futtatása |32 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Újrapróbálkozások száma a folyamathoz tartozó tevékenységek futtatásához |1,000 |MaxInt (32 bit) |

<sup>1</sup> a folyamat, az adathalmaz és a társított szolgáltatás objektumok a számítási feladatok logikai csoportosítását jelölik. Ezeknek az objektumoknak a korlátai nem kapcsolódnak a Azure Data Factory használatával áthelyezhető és feldolgozható adatmennyiséghez. Data Factory úgy van kialakítva, hogy az petabájt kezeljen.

<sup>2</sup> az igény szerinti HDInsight magok az adatelőállítót tartalmazó előfizetésből vannak kiosztva. Ennek eredményeképpen az előző korlát az igény szerinti HDInsight magok Data Factory által kényszerített alapszintű korlátja. Ez eltér az Azure-előfizetéséhez társított alapszinttől.

<sup>3</sup> az 1. verzióhoz tartozó Cloud adatáthelyezési egység (DMU) felhőből felhőbe másolási műveletben használatos, további információ a [Felhőbeli adatáthelyezési egységekről (1. verzió)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). További információ a számlázásról: [Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

| **Erőforrás** | **Alapértelmezett alsó korlát** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Újrapróbálkozási kísérletek közötti intervallum |1 másodperc |1 másodperc |
| Újrapróbálkozás időtúllépési értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webszolgáltatás-hívások korlátai
A Azure Resource Manager API-hívásokra vonatkozó korlátozásokkal rendelkezik. Az API-hívásokat a [Azure Resource Manager API korlátain](../articles/azure-subscription-service-limits.md#resource-group-limits)belül is elvégezheti.

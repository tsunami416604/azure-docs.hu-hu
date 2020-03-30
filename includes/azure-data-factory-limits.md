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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086140"
---
Az Azure Data Factory egy több-bérlős szolgáltatás, amely rendelkezik a következő alapértelmezett korlátokat, hogy az ügyfél-előfizetések védettegymás számítási feladatok. Ha az előfizetésre vonatkozó maximális korlátot szeretné megemelni, forduljon az ügyfélszolgálathoz.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Adatgyárak egy Azure-előfizetésben | 800 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Entitások, például folyamatok, adatkészletek, eseményindítók, összekapcsolt szolgáltatások és integrációs futásidők teljes száma egy adat-előállítón belül | 5000 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Az Azure-SSIS-integrációs futásidők összes processzormagja egyetlen előfizetés sel | 256 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű folyamat fut egy adat-gyár, amely megosztott a gyárösszes folyamatok a gyárban | 10,000  | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű külső tevékenység előfizetésenként fut az [Azure-integrációs futásidejű régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>A külső tevékenységek kezelése integrációs futásidőben, de a kapcsolódó szolgáltatások, beleértve a Databricks, tárolt eljárás, HDInsights, web és mások.</small> | 3000 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű folyamattevékenység előfizetésenként fut az [Azure-integrációs futásidejű régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>A folyamattevékenységek integrációs futásidejűek, például a Lookup, a GetMetadata és a Delete.</small>| 1000 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű szerzői műveletek előfizetésenként [Azure-integrációs futásidejű régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Beleértve a tesztkapcsolatot, a mappalista és a táblázatlista tallózását, az adatok előnézetét. | 200 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű adatintegrációs egységek<sup>előfizetésenként 1</sup> felhasználás [azure-integrációs futásidejű régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Régió csoport 1<sup>2</sup>: 6000<br>Régió csoport 2<sup>2</sup>: 3000<br>Régió csoport 3<sup>2</sup>: 1500 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| A tevékenységek maximális futtatása folyamatonként, amely magában foglalja a tárolók belső tevékenységeit | 40 | 40 |
| Egyetlen önkiszolgáló integrációs futásidő vel létrehozható csatolt integrációs futásidők maximális száma | 100 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Maximális paraméterek folyamatonként | 50 | 50 |
| ForEach-elemek | 100 000 | 100 000 |
| ForEach párhuzamosság | 20 | 50 |
| Folyamatos sorok ban futó futtatások maximális futtatása folyamatonként | 100 | 100 |
| Karakterek kifejezésenként | 8,192 | 8,192 |
| Minimális bukdácsoló ablak eseményindító-időköz | 15 min | 15 min |
| A folyamattevékenység-futtatások maximális időtúllépése | 7 nap | 7 nap |
| Folyamatobjektumok objektumonkénti bájtjai<sup>3</sup> | 200 KB | 200 KB |
| Objektumonkénti bájt adatkészlethez és csatolt szolgáltatásobjektumokhoz<sup>3</sup> | 100 KB | 2000 KB |
| Adatintegrációs egységek<sup>1</sup> másolási tevékenységfuttatásonként | 256 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| API-hívások írása | 1 200/h<br/><br/> Ezt a korlátot az Azure Resource Manager, nem az Azure Data Factory szabja ki. | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| API-hívások olvasása | 12 500/h<br/><br/> Ezt a korlátot az Azure Resource Manager, nem az Azure Data Factory szabja ki. | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Lekérdezések figyelése percenként | 1,000 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Entitás CRUD műveletek percenként | 50 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Az adatfolyam-hibakeresési munkamenet maximális ideje | 8 óra | 8 óra |
| Az adatfolyamok egyidejű száma gyárilag | 50 | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű számú adatfolyam-hibakeresési munkamenet felhasználónként és gyárilag | 3 | 3 |
| Adatfolyam Az Azure infravörös TTL-korlát | 4 óra | [Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |

<sup>1</sup> Az adatintegrációs egység (DIU) felhőből felhőbe történő másolási műveletben használatos, többet az [adatintegrációs egységekből (2. verzió)](../articles/data-factory/copy-activity-performance.md#data-integration-units). A számlázással kapcsolatos további információkért lásd: [Azure Data Factory díjszabása.](https://azure.microsoft.com/pricing/details/data-factory/)

<sup>2</sup> [Az Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) [globálisan elérhető](https://azure.microsoft.com/global-infrastructure/services/) az adatok megfelelőségének, hatékonyságának és a hálózati kimenő forgalom költségeinek csökkentése érdekében. 

| Régiócsoport | Régiók | 
| -------- | ------ |
| 1. régiócsoport | USA középső, USA keleti, USA keleti, Észak-Európa, Nyugat-Európa, USA nyugati régiója, USA nyugati régiója 2 |
| 2. régiócsoport | Kelet-Ausztrália, Délkelet-Ausztrália, Brazília Déli, Közép-India, Japán-kelet, USA északnyugati része, USA délnyugati régiója, Délkelet-Ázsia, USA nyugati középső régiója |
| 3. régiócsoport | Kanada Közép-, Kelet-Ázsia, Franciaország Közép-, Korea Közép-, Egyesült Királyság Dél |

<sup>3</sup> A folyamat, az adatkészlet és a csatolt szolgáltatásobjektumok a munkaterhelés logikai csoportosítását jelentik. Ezek az objektumok korlátai nem kapcsolódnak az Azure Data Factory segítségével áthelyezhető és feldolgozható adatok mennyiségéhez. A Data Factory úgy lett kialakítva, hogy a petabájtnyi adatot kezelje.

### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Maximális határérték** |
| --- | --- | --- |
| Adat-előállító folyamatok |2500 |[Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Adatkészletek adatelén belül |5000 |[Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Egyidejű szeletek adatkészletenként |10 |10 |
| Folyamatobjektumok objektumonkénti bájtjai<sup>1</sup> |200 KB |200 KB |
| Objektumonkénti bájt adathalmazhoz és csatolt szolgáltatásobjektumokhoz<sup>1</sup> |100 KB |2000 KB |
| Az Azure HDInsight igény szerinti fürtmagok egy előfizetésen belül<sup>2</sup> |60 |[Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| A felhőalapú adatok mozgása ido-nkénti futtatás<sup>3</sup> |32 |[Lépjen kapcsolatba az ügyfélszolgálattal.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Újrapróbálkozások száma a folyamatfolyamatok futtatásaihoz |1,000 |MaxInt (32 bites) |

<sup>1</sup> A folyamat, az adatkészlet és a csatolt szolgáltatásobjektumok a munkaterhelés logikai csoportosítását jelentik. Ezek az objektumok korlátai nem kapcsolódnak az Azure Data Factory segítségével áthelyezhető és feldolgozható adatok mennyiségéhez. A Data Factory úgy lett kialakítva, hogy a petabájtnyi adatot kezelje.

<sup>2</sup> Igény szerinti HDInsight magok vannak lefoglalva az adat-előállítót tartalmazó előfizetésből. Ennek eredményeképpen az előző korlát a Data Factory által kényszerített core korlát az igény szerinti HDInsight magok. Ez eltér az Azure-előfizetéshez társított alapkorláttól.

<sup>3</sup> A felhőalapú adatmozgatási egység (DMU) az 1-es verzióhoz felhőből felhőbe történő másolási műveletben használatos, többet a [felhőbeli adatmozgatási egységekből (1. verzió)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). A számlázással kapcsolatos további információkért lásd: [Azure Data Factory díjszabása.](https://azure.microsoft.com/pricing/details/data-factory/)

| **Erőforrás** | **Alapértelmezett alsó határérték** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Az újrapróbálkozások közötti időköz |1 másodperc |1 másodperc |
| Újrapróbálkozási időtúlérték |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webszolgáltatás hívási korlátai
Az Azure Resource Manager korlátozza az API-hívásokat. Az [Azure Resource Manager API-korlátain](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)belül api-hívásokat kezdeményezhet.

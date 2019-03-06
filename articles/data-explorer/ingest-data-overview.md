---
title: Az Azure Data Explorer adatbetöltés
description: A különböző módjait, betöltheti az adatok az Azure Data Explorer (betöltése)
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 2/18/2019
ms.openlocfilehash: bd1cb101a3bf9a4da9c681169d2a5c999e56b1b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438414"
---
# <a name="azure-data-explorer-data-ingestion"></a>Az Azure Data Explorer adatbetöltés

Adatbetöltés betölteni az adatfelderítési rekordok létrehozásához vagy frissítéséhez az Adatkezelőben az Azure tábla egy vagy több forrásból származó használja a rendszer. Betöltött, miután az adatok lekérdezés számára elérhetővé válik. Az alábbi ábrán látható a teljes körű folyamatot az Azure az adatkezelőt, beleértve az adatbetöltés használatához.

![Az adatfolyam](media/ingest-data-overview/data-flow.png)

Az Azure Data Explorer data management szolgáltatást, ami felelős az adatbetöltés, az alábbi funkciókat biztosítja:

1. **Adatok lekéréses**: Adatok lekérése külső forrásból (az Event Hubs), vagy olvassa el a feldolgozási kérelmek egy Azure-üzenetsorból.

1. **Kötegelés**: A Batch adatforgalmát ugyanazon adatbázis és tábla a betöltési teljesítmény optimalizálása.

1. **Érvényesítési**: Előzetes ellenőrzési és formátum konvertálása szükség esetén.

1. **Adatkezelés**: Egyező séma, rendezése, az indexelés, kódolási és tömöríti az adatokat.

1. **A betöltési folyamat adatmegőrzés pont**: A motor a feldolgozási terhelés kezelése, és kezelheti az újrapróbálkozásokat átmeneti hibák esetén.

1. **Az adatok betöltését véglegesítési**: Elérhetővé teszi a lekérdezéshez.

## <a name="ingestion-methods"></a>Adatbetöltési módszerek

Az Azure Data Explorer Adatbetöltési több módszert, mindegyiket a saját cél forgatókönyvek előnyeit és hátrányait támogatja. Az Azure Data Explorer folyamatok és a közös szolgáltatásokat, az SDK-k és közvetlen hozzáférést a motor feltárás célokra programozott Adatbetöltési összekötők érhetők el.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Betöltési folyamatok, összekötők és beépülő modulok használata

Az Azure Data Explorer jelenleg támogatja:

* Event Grid folyamatot, amely a felügyeleti varázslója használatával az Azure Portalon lehet kezelni. További információkért lásd: [Azure-Blobok feldolgozása az Azure Data Explorer](ingest-data-event-grid.md).

* Event Hub folyamatot, amely a felügyeleti varázslója használatával az Azure Portalon lehet kezelni. További információkért lásd: [betölteni az adatokat az Event Hubs az Azure Data Explorer](ingest-data-event-hub.md).

* Logstash beépülő modult, tekintse meg [kiolvasni az adatokat az Azure Adatkezelőbe Logstash](ingest-data-logstash.md).

* A Kafka-összekötőt, lásd: [betölteni az adatokat a Kafkából az Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Adatbetöltési integrációs szolgáltatások használata

* Az Azure Data Factory (ADF), egy teljes körűen felügyelt adatintegrációs szolgáltatás az Azure-ban, az adatok másolásához, és az Azure Data Explorer elemzési számítási feladatokhoz. További információkért lásd: [másolja az adatokat, vagy Azure Data Factory használatával az Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer).

### <a name="programmatic-ingestion"></a>Programozott adatfeldolgozást

Az adatkezelő az Azure SDK-k, amelyek lekérdezési és adatfeldolgozási használható biztosít. Programozott Adatbetöltési csökkenthetik a tárelérési tranzakciók során, és a betöltési folyamat a következő feldolgozási költségeit (COGs), optimalizáltuk.

**Elérhető SDK-kkal és a nyílt forráskódú projektek**:

Kusto ügyféloldali SDK-val kell fogadni és kérdezhet le adatokat a felhasználható kínálja:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programozott feldolgozási technikákat**:

* Tölt be adatot az Azure Data Explorer data management szolgáltatás (nagy átviteli sebességű és megbízható Adatbetöltési) keresztül:

    [**Batch-betöltési** ](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (SDK által rendelkezésre bocsátott): az ügyfél feltölti az adatokat az Azure Blob storage (az Azure Data Explorer data management szolgáltatás által jelölt) és a egy Azure-üzenetsor értesítést küld bejegyzést. Kötegelt feldolgozási, az ajánlott módszer a nagy mennyiségű, megbízható, és a költséghatékony adatbetöltés.

* Adatok bevitele az Azure Data Explorer motor (leginkább megfelelő adatáttekintési és prototípus-készítéshez) közvetlenül be:

  * **Beágyazott Adatbetöltési**: vezérlési parancsot (.ingest beágyazott), a sávon kívüli adatokat tartalmazó alkalmi tesztelési célra szól.

  * **A lekérdezés betöltését**: vezérlési parancsot (.set, .set vagy hozzáfűző, .set vagy lecserélése) lekérdezés eredményeit mutató jelentéseket vagy kis ideiglenes táblák létrehozásához szolgál.

  * **Storage-ból betöltési**: külsőleg tárolt adatok (például az Azure Blob Storage)-vezérlő parancsot (.ingest be) lehetővé teszi, hogy az adatok hatékony tömeges betöltési.

**Különböző módszerekkel késését**:

| Módszer | Késés |
| --- | --- |
| **Beágyazott adatfeldolgozást** | Azonnali |
| **A lekérdezés feldolgozása** | Lekérdezési idő + feldolgozási idő |
| **Betöltési storage-ból** | Letöltési idő + feldolgozási idő |
| **Az aszinkron feldolgozási** | Kötegelés idő + feldolgozási idő |
| |

Az adatok mérete kisebb, mint néhány másodpercig függ feldolgozási ideje. A kötegelés idő az alapértelmezett érték 5 perc.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Az Adatbetöltési leginkább megfelelő módszer kiválasztása

Adatok betöltése előtt kérdezze meg magának az alábbi kérdésekre.

* Ahol nem találhatók az adataimat? 
* Mi az az adatok formátumát, és ez módosítható? 
* Mik azok a szükséges mezőket le kell kérdezni? 
* Mi az a várt adatok mennyisége és a sebesség? 
* Hány eseménytípusok (ezt a számot a táblák) várható? 
* Milyen gyakran várható módosítása a eseménysémája? 
* Hány csomópontot hoz létre az adatok? 
* Mi az a forrás operációs rendszer? 
* Mik a késési követelményei? 
* A meglévő felügyelt streamfeldolgozási folyamatok egyik használható? 

A szervezet számára a meglévő infrastruktúra, amely egy üzenetküldési szolgáltatást, például az Event Hubs alapuló összekötőjének használatával, valószínűleg a legmegfelelőbb megoldás. Az aszinkron feldolgozási alkalmas nagy méretű adatkötetek esetében.

## <a name="supported-data-formats"></a>Támogatott adatformátumok a célnyelven

Az összes támogatunk módszerek nem betöltési lekérdezésből formázza az adatokat, hogy az Azure Data Explorer értelmezni tudja. A támogatott formátumok a következők:

* A CSV, TSV, PSV, SCSV ÉS RENDSZERÁLLAPOT-VISSZAJELZÉS:
* JSON (sor elválasztott, többsoros), Avro
* ZIP és GZIP 

> [!NOTE]
> Adatok betöltése folyamatban van, amikor az adattípusok vannak következtetni a céloldali tábla oszlopait alapján. Ha egy rekord nem fejeződött be, vagy egy mezőt a szükséges adatok típusa nem elemezhető, a megfelelő tábla oszlopait elkészül a null értékű.

## <a name="ingestion-recommendations-and-limitations"></a>Adatbetöltési javaslatok és korlátozások

* A tényleges megőrzési házirend betöltött az adatbázis adatmegőrzési származik. Lásd: [adatmegőrzési](/azure/kusto/concepts/retentionpolicy) részleteiről. Adatok bevitele szükséges **tábla módon eredményesen dolgozható** vagy **adatbázis módon eredményesen dolgozható** engedélyeket.
* Adatbetöltési 5 GB-os maximális fájlméret támogatja. A javaslat, hogy fájlokat 100 MB-os és 1 GB között.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Séma-hozzárendelés segít a céloldali tábla oszlopait forrás datová Pole kötni.

* [CSV-leképezés](/azure/kusto/management/mappings?branch=master#csv-mapping) sorszámát-alapú eljuttathatók (nem kötelező) együttműködik. A Betöltés parancs paraméter használatával elvégezhető vagy [előre létrehozni a következő táblán](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) és a betöltés parancsparaméter hivatkozott.
* [JSON-leképezés](/azure/kusto/management/mappings?branch=master#json-mapping) (kötelező) és [Avro leképezés](/azure/kusto/management/mappings?branch=master#avro-mapping) (kötelező) a betöltés parancssori paraméter segítségével hajtható végre. Lehet is [előre létrehozni a következő táblán](/azure/kusto/management/tables#create-ingestion-mapping) és a betöltés parancsparaméter hivatkozott.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Betölteni az adatokat az Event Hubs az Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Az Adatkezelőben az Azure Event Grid-előfizetés használata az adatok betöltése](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Betölteni az adatokat a Kafkából az Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Az Azure Data Explorer Python-kódtár használata az adatok betöltése](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Az Azure Data Explorer csomópontja library használata az adatok betöltése](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Adatokat az Azure SDK-val Data Explorer .NET Standard (előzetes verzió)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Betöltési adat a Logstashből az Adatkezelőbe az Azure](ingest-data-logstash.md)

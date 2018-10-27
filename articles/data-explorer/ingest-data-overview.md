---
title: Az Azure Data Explorer adatbetöltés
description: A különböző módjait, betöltheti az adatok az Azure Data Explorer (betöltése)
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f1df22c505bffdfaf60bf9c6eec3ad4e698fff02
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139526"
---
# <a name="azure-data-explorer-data-ingestion"></a>Az Azure Data Explorer adatbetöltés

Adatbetöltés betölteni az adatfelderítési rekordok létrehozásához vagy frissítéséhez az Adatkezelőben az Azure tábla egy vagy több forrásból származó használja a rendszer. Betöltött, miután az adatok lekérdezés számára elérhetővé válik. Az alábbi ábra bemutatja a teljes körű folyamatot az Azure az adatkezelőt, beleértve az adatbetöltés használatához **(2)**.

![A teljes folyamat](media/ingest-data-overview/overall-data-flow.png)

Az Azure Data Explorer data management szolgáltatást, ami felelős az adatbetöltés, az alábbi funkciókat biztosítja:

1. **Adatok lekéréses**: adatok lekérése külső forrásból (az Event Hubs), vagy olvassa el a feldolgozási kérelmek egy Azure-üzenetsorból.

1. **Kötegelés**: Batch-adatbázis és a tábla a betöltési teljesítmény optimalizálása adatforgalmát.

1. **Érvényesítési**: előzetes ellenőrzés és formátum átalakítására, szükség esetén.

1. **Adatkezelés**: egyező séma, rendezése, az indexelés, kódolási és tömöríti az adatokat.

1. **A betöltési folyamat adatmegőrzés pont**: átmeneti hibák motor és leíró megismétlését feldolgozási terhelés kezeléséhez.

1. **Az adatok betöltését véglegesítési**: lekérdezés elérhetővé teszi.

> [!NOTE]
> A tényleges megőrzési házirend betöltött az adatbázis adatmegőrzési származik. Lásd: [adatmegőrzési](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy) részleteiről. Adatok bevitele szükséges **tábla módon eredményesen dolgozható** vagy **adatbázis módon eredményesen dolgozható** engedélyeket.

## <a name="ingestion-methods"></a>Adatbetöltési módszerek

Az Azure Data Explorer Adatbetöltési több módszert, mindegyiket a saját cél forgatókönyvek előnyeit és hátrányait támogatja. Az Azure Data Explorer közös szolgáltatásokat, az SDK-k és közvetlen hozzáférést a motor feltárás célokra programozott Adatbetöltési összekötőket biztosít.

### <a name="ingestion-using-connectors"></a>Adatbetöltési összekötők használatával

Az Azure Data Explorer jelenleg támogatja az Event Hub-összekötőt, amely a varázsló segítségével az Azure Portalon lehet kezelni. További információkért lásd: [a rövid útmutató: betölteni az adatokat az Event Hubs az Azure Data Explorer](ingest-data-event-hub.md).

### <a name="programmatic-ingestion"></a>Programozott adatfeldolgozást

Az adatkezelő az Azure SDK-k, amelyek lekérdezési és adatfeldolgozási használható biztosít. Programozott Adatbetöltési csökkenthetik a tárelérési tranzakciók során, és a betöltési folyamat a következő feldolgozási költségeit (COGs), optimalizáltuk.

**Elérhető SDK-kkal és a nyílt forráskódú projektek**:

Kusto ügyféloldali SDK-val kell fogadni és kérdezhet le adatokat a felhasználható kínálja:

* [Python SDK](https://docs.microsoft.com/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](https://docs.microsoft.com/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](https://docs.microsoft.com/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK]

* [REST API](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programozott feldolgozási technikákat**:

* Tölt be adatot az Azure Data Explorer data management szolgáltatás (nagy átviteli sebességű és megbízható Adatbetöltési) keresztül

  * [**Batch-betöltési** ](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (SDK által rendelkezésre bocsátott): az ügyfél feltölti az adatokat az Azure Blob storage (az Azure Data Explorer data management szolgáltatás által jelölt) és a egy Azure-üzenetsor értesítést küld bejegyzést. Ez a nagy mennyiségű, megbízható és költséghatékony Adatbetöltési a javasolt módszer.

* Adatok bevitele az Azure Data Explorer motor (leginkább megfelelő adatáttekintési és prototípus-készítéshez) közvetlenül be:

  * **Beágyazott Adatbetöltési**: vezérlési parancsot (.ingest beágyazott), a sávon kívüli adatokat tartalmazó alkalmi tesztelési célokra szolgál.

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

Feldolgozási idő attól függ, az adatok mérete általában kevesebb, mint néhány másodpercig. A kötegelés idő az alapértelmezett érték 5 perc.

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

Egy üzenetkezelési szolgáltatást, például az Event Hubs alapján egy meglévő infrastruktúra szervezetekre vonatkozó összekötőjének használatával, valószínűleg a legmegfelelőbb megoldás. Az aszinkron feldolgozási alkalmas nagy méretű adatkötetek esetében.

## <a name="supported-data-formats"></a>Támogatott adatformátumok a célnyelven

Metódusok nem betöltési lekérdezésből származó összes támogatunk az adatok formázni kell, a támogatott formátumok egyikében, hogy az Azure Data Explorer értelmezni tudja.

* A CSV, TSV, PSV, SCSV ÉS RENDSZERÁLLAPOT-VISSZAJELZÉS:
* JSON (sor elválasztott, többsoros), Avro
* ZIP és GZIP 

> [!NOTE]
> Adatok betöltése folyamatban van, amikor az adattípusok vannak következtetni a céloldali tábla oszlopait alapján. Ha egy rekord nem fejeződött be, vagy egy mezőt a szükséges adatok típusa nem elemezhető, a megfelelő tábla oszlopait elkészül a null értékű.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Séma-hozzárendelés segít a determinisztikus módon kötése forrás datová Pole céloldali tábla oszlopait.

* [CSV-leképezés](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#csv-mapping) (nem kötelező) együttműködik minden sorszámát alapú formátumok és a betöltés parancsparaméter argumentumként átadhatók vagy [előre létrehozni a következő táblán](https://docs.microsoft.com/azure/kusto/management/tables?branch=master#create-ingestion-mapping) és a betöltés parancsparaméter hivatkozott.
* [JSON-leképezés](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#json-mapping) (kötelező) és [Avro leképezés](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#avro-mapping) (kötelező) a betöltés parancsparaméter argumentumként átadhatók vagy [előre létrehozni a következő táblán](https://docs.microsoft.com/azure/kusto/management/tables#create-ingestion-mapping) és a betöltés parancsparaméter hivatkozott.

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Adatok betöltése az Event Hubsból az Azure Data Explorerbe](ingest-data-event-hub.md)

[Rövid útmutató: Adatok betöltése az Azure Data Explorer Python-kódtárral](python-ingest-data.md)


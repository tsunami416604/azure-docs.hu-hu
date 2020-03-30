---
title: Az Azure Data Explorer adatbetöltése
description: Ismerje meg, hogy milyen különböző módokon töltheti be (töltheti be) az adatokat az Azure Data Explorerben
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246395"
---
# <a name="azure-data-explorer-data-ingestion"></a>Az Azure Data Explorer adatbetöltése

Az adatok betöltése az a folyamat, amely egy vagy több forrásból származó adatrekordok betöltésére szolgál egy tábla létrehozásához vagy frissítéséhez az Azure Data Explorerben. A betöltésután az adatok elérhetővé válnak a lekérdezéshez. Az alábbi ábrán látható a végpontok között az Azure Data Explorer ben végzett munka folyamata, beleértve az adatbetöltést is.

![Adatfolyam](media/ingest-data-overview/data-flow.png)

Az Azure Data Explorer adatkezelési szolgáltatása, amely az adatok betöltésével felelős, a következő funkciókat biztosítja:

1. **Adatlekérések:** Adatok lekérése külső forrásokból (Event Hubs) vagy olvasási kérelmek egy Azure-várólistából.

1. **Kötegelés:** Kötegelt adatok áramlik ugyanabba az adatbázisba és táblába a betöltési átviteli szint optimalizálása érdekében.

1. **Érvényesítés**: Előzetes érvényesítés és formátumátalakítás, ha szükséges.

1. **Adatkezelés**: Az adatok egyeztetése, séma rendezése, indexelése, kódolása és tömörítése.

1. **Perzisztencia pont a betöltési folyamat:** A motor betöltési terhelésének kezelése és átmeneti hibák esetén az újrapróbálkozások kezelése.

1. **Az adatok betöltésének véglegesítése**: Elérhetővé teszi az adatokat a lekérdezésszámára.

## <a name="ingestion-methods"></a>Lenyelési módszerek

Az Azure Data Explorer számos betöltési módszert támogat, amelyek mindegyike saját célforgatókönyvekkel, előnyökkel és hátrányokkal rendelkezik. Az Azure Data Explorer folyamatokat és összekötőket kínál a közös szolgáltatásokhoz, az SDK-k használatával történő programozott betöltést, valamint a motorhoz való közvetlen hozzáférést feltárási célokra.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Betöltés csővezetékek, csatlakozók és bővítmények használatával

Az Azure Data Explorer jelenleg a következőket támogatja:

* Event Grid-folyamat, amely az Azure Portal felügyeleti varázslójával kezelhető. További információ: [Az Azure-blobok betöltése az Azure Data Explorerbe](ingest-data-event-grid.md)című témakörben található.

* Event Hub-folyamat, amely az Azure Portal felügyeleti varázslójával kezelhető. További információ: [Adatok betöltése az Event Hubról az Azure Data Explorerbe című témakörben.](ingest-data-event-hub.md)

* Logstash beépülő modul, [lásd: Adatok betöltése a Logstash-ból az Azure Data Explorerbe.](ingest-data-logstash.md)

* Kafka-összekötő, [lásd: A Kafka adatainak betöltése az Azure Data Explorerbe.](ingest-data-kafka.md)

### <a name="ingestion-using-integration-services"></a>Betöltés integrációs szolgáltatások használatával

* Az Azure Data Factory (ADF), egy teljes körűen felügyelt adatintegrációs szolgáltatás az Azure-beli analitikus számítási feladatokhoz, hogy adatokat másoljon az Azure Data Explorerbe és onnan [a támogatott adattárak és formátumok](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)használatával. További információ: [Adatok másolása az Azure Data Factoryból az Azure Data Explorer be című témakörben.](/azure/data-explorer/data-factory-load-data)

### <a name="programmatic-ingestion"></a>Programozott lenyelés

Az Azure Data Explorer sdk-ket biztosít, amelyek lekérdezési és adatbetöltési használható. A programozott betöltés a betöltési költségek (COG-k) csökkentésére van optimalizálva, a tárolási tranzakciók minimalizálásával a betöltési folyamat során és azt követően.

**Elérhető SDK-k és nyílt forráskódú projektek:**

A Kusto ügyfélSDK-t kínál, amely az adatok betöltésére és lekérdezésére használható:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programozott lenyelési technikák:**

* Adatok betöltése az Azure Data Explorer adatkezelési szolgáltatásán keresztül (nagy átviteli sebességű és megbízható betöltés):

    [**Kötegelt betöltés**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (az SDK által biztosított): az ügyfél feltölti az adatokat az Azure Blob storage -ba (amelyet az Azure Data Explorer adatkezelési szolgáltatás ad ki), és értesítést küld egy Azure-várólistába. A kötegelt feldolgozás az ajánlott technika a nagy mennyiségű, megbízható és olcsó adatbetöltéshez.

* Adatok közvetlen betöltése az Azure Data Explorer motorjába (a feltáráshoz és prototípus-készítéshez leginkább megfelelő):

  * **Inline betöltés:** a sávon ként adatokat tartalmazó vezérlőparancs (.inline) ad hoc tesztelési célokra szolgál.

  * **Betöltés lekérdezés:** vezérlőparancs (.set, .set-or-append, .set-or-replace), amely a lekérdezési eredményekre mutat, jelentések vagy kis ideiglenes táblák létrehozására szolgál.

  * **Betöltés a tárolóból:** control command (.ingt be) a külsőleg tárolt adatokkal (például az Azure Blob Storage) lehetővé teszi az adatok hatékony tömeges betöltését.

**A különböző módszerek késése:**

| Módszer | Késés |
| --- | --- |
| **Inline lenyelés** | Azonnali |
| **Betöltés lekérdezésből** | Lekérdezési idő + feldolgozási idő |
| **Betöltés a tárolóból** | Letöltési idő + feldolgozási idő |
| **Várólistára helyezett betöltés** | Kötegelési idő + feldolgozási idő |
| |

A feldolgozási idő az adatok méretétől függ, néhány másodpercnél rövidebb ideig. A kötegelési idő alapértelmezés szerint 5 perc.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>A legmegfelelőbb lenyelési módszer kiválasztása

Mielőtt elkezdené beadni az adatokat, tegye fel magának a következő kérdéseket.

* Hol laknak az adataim? 
* Mi az adatformátum, és meg lehet-e változtatni? 
* Melyek a szükséges mezők lekérdezése? 
* Mi a várt adatmennyiség és -sebesség? 
* Hány eseménytípusra kell számítani (a táblák számaként jelenik meg)? 
* Milyen gyakran várható az eseményséma módosítása? 
* Hány csomópont hozza létre az adatokat? 
* Mi a forrás operációs rendszer? 
* Mik a késési követelmények? 
* Használható-e a meglévő felügyelt betöltési folyamatok egyike? 

A meglévő infrastruktúrával rendelkező szervezetek, amelyek egy üzenetküldő szolgáltatás, például az Event Hub és az IoT Hub, egy összekötő használata valószínűleg a legmegfelelőbb megoldás. A várakozási sorba állított betöltés nagy adatkötetek esetén megfelelő.

## <a name="supported-data-formats"></a>Támogatott adatformátumok

A lekérdezésből történő betöltésen kívül minden betöltési módszer esetében formázza az adatokat úgy, hogy az Azure Data Explorer elemezhesse azokat. 
* A támogatott adatformátumok a következők: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (vonal-elválasztott, többsoros), Avro, Orc és Parketta. 
* Támogatja a ZIP és GZIP tömörítést.

> [!NOTE]
> Az adatok betöltése során az adattípusok a céltábla oszlopai alapján következnek. Ha egy rekord hiányos, vagy egy mező nem elemezhető szükséges adattípusként, a megfelelő táblaoszlopok at null értékekkel tölti fel a rendszer.

## <a name="ingestion-recommendations-and-limitations"></a>A lenyelési javaslatok és korlátozások

* A bevitt adatok hatékony adatmegőrzési szabálya az adatbázis adatmegőrzési házirendjéből származik. A részleteket az [adatmegőrzési szabályzatban](/azure/kusto/concepts/retentionpolicy) találja. Az adatok betöltéséhez **táblabetöltési** vagy **adatbázis-betöltési** engedélyek szükségesek.
* A betöltés legfeljebb 5 GB-os fájlméretet támogat. A javaslat a 100 MB és 1 GB közötti fájlok betöltése.

## <a name="schema-mapping"></a>Sémaleképezés

A sémaleképezés segít a forrásadatmezők nek a céltábla oszlopaihoz való kötéséhez.

* [A CSV-leképezés](/azure/kusto/management/mappings?branch=master#csv-mapping) (nem kötelező) az összes sorszámalapú formátummal működik. Ez a betöltési parancs paraméter használatával hajtható végre, vagy [előre létrehozott a táblában,](/azure/kusto/management/create-ingestion-mapping-command) és hivatkozott a betöltési parancs paraméter.
* [JSON mapping](/azure/kusto/management/mappings?branch=master#json-mapping) (kötelező) és [Avro leképezés](/azure/kusto/management/mappings?branch=master#avro-mapping) (kötelező) a betöltési parancs paraméter használatával hajtható végre. Ők is [előre létre a táblában,](/azure/kusto/management/create-ingestion-mapping-command) és hivatkozott a betöltési parancs paraméter.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatok betöltése az Event Hubról az Azure Data Explorerbe](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Adatok betöltése az Event Grid-előfizetéssel az Azure Data Explorerbe](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Adatok betöltése a Kafkából az Azure Data Explorerbe](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Adatok betöltése az Azure Data Explorer Python-tár használatával](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Adatok betöltése az Azure Data Explorer csomópontkönyvtárával](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Adatok betöltése az Azure Data Explorer .NET Standard SDK (előzetes verzió) használatával](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Adatok betöltése a Logstash szolgáltatásból az Azure Data Explorerbe](ingest-data-logstash.md)

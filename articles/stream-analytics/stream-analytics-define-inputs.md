---
title: Azure Stream Analytics a bemenő Stream az adatok
description: Ismerje meg az Azure Stream Analyticsben adatkapcsolat beállítása. Bemeneti események származó adatok adatfolyamot, és referenciaadatok is.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392551"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream data bemeneti Stream analyticsbe

Stream Analytics bemenetként három típusú erőforrásokat az Azure data-adatfolyamok első osztályú integrálva van:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Ezen a bemeneti források is élő Azure-előfizetéshez, a Stream Analytics-feladat vagy egy másik előfizetésben található.

### <a name="compression"></a>Tömörítés

Stream Analytics támogatja a tömörítést minden stream bemeneti adatforrás esetében. A támogatott tömörítési típusok a következők: none, GZip és deflate Compression. A tömörítés támogatása nem érhető el a referenciaadatoknál. Ha a bemeneti formátum az Avro-adatok tömörített, azt transzparens módon történik. Adja meg a tömörítési típus szerializálása az Avro-nincs szükségünk. 

## <a name="create-edit-or-test-inputs"></a>Létrehozásához, szerkesztéséhez vagy bemenet tesztelése

A [Azure Portal](stream-analytics-quick-create-portal.md), a [Visual Studio](stream-analytics-quick-create-vs.md)és a [Visual Studio Code](quick-create-vs-code.md) használatával hozzáadhat és megtekintheti vagy szerkesztheti a meglévő bemeneteket a folyamatos átviteli feladatban. A Azure Portal, a [Visual Studio](stream-analytics-vs-tools-local-run.md)és a [Visual Studio Code](visual-studio-code-local-run.md)használatával is tesztelheti a bemeneti kapcsolatokat és tesztelheti a mintavételezési [lekérdezéseket](stream-analytics-manage-job.md#test-your-query) . A lekérdezés írásakor megjelenik a FROM záradékban szereplő bemenet. Az elérhető bemenetek listáját a portál **lekérdezés** lapján érheti el. Ha több bemenetet szeretne használni, `JOIN`heti vagy több `SELECT` lekérdezést is írhat.


## <a name="stream-data-from-event-hubs"></a>Adatok streamelése az Event Hubsból

Az Azure Event Hubs nyújt hatékonyan méretezhető közzétételi és előfizetési esemény ingestors. Az Event hub másodpercenként akár több millió eseményt is gyűjthet, így a csatlakoztatott eszközök és alkalmazások által előállított nagy mennyiségű adatot feldolgozhatja és elemezheti. Együtt, az Event Hubs és a Stream Analytics egy teljes körű megoldást biztosítanak valós idejű elemzés. Az Event Hubs lehetővé teszi az Azure-ba való hírcsatorna-esemény valós idejű, és a Stream Analytics-feladatok valós idejű események feldolgozására képes. Például elküldheti webes kattintással, érzékelőinek vagy online alkalmazásnapló-események az Event hubs szolgáltatásba. Ezután létrehozhat Stream Analytics-feladatok a bemeneti adatok Streamek valós idejű szűrés, összesítenie és korrelációs az Event Hubs adatokként.

`EventEnqueuedUtcTime` az esemény egy esemény-központba való érkezésének időbélyege, és a Event Hubsról Stream Analyticsre érkező események alapértelmezett időbélyegzője. Ha az adatokat adatfolyamként szeretné feldolgozni az esemény hasznos adatait tartalmazó időbélyeget használva, az [időbélyeget kulcsszó szerint](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kell használnia.

### <a name="event-hubs-consumer-groups"></a>Event Hubs fogyasztói csoportok

Be kell állítania minden Stream Analytics eseményközpont szeretné, hogy a saját fogyasztói csoportot adjon meg. Amikor egy feladat önillesztést tartalmaz, vagy rendelkezik több bemenet, néhány bemenetei között meg egynél több olvasó aktiválásához előfordulhat, hogy olvashatják. Ebben a helyzetben befolyásolja az olvasók egyetlen felhasználói csoportban. Az Event Hubs túllépő / partíciónként fogyasztói csoportot öt olvasók elkerüléséhez célszerű minden Stream Analytics-feladat egy fogyasztói csoportot kijelölje. A standard szintű Event hub esetében 20 fogyasztói csoport is engedélyezett. További információ: [Azure stream Analytics bemenetek hibáinak megoldása](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Bemenet létrehozása Event Hubsból

Az alábbi táblázat a Azure Portal **új bemeneti** lapján lévő összes tulajdonságot ismerteti egy Event hub adatbemenetének adatfolyamként történő továbbításához:

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** |Egy rövid nevet, amely hivatkozik a bemeneti használatával a feladat lekérdezésben. |
| **Előfizetés** | Válassza ki az előfizetést, amelyben az Event hub-erőforrás található. | 
| **Event hub-névtér** | Az Event Hubs-névtér az üzenetküldési entitások készletének tárolója. Egy új eseményközpont létrehozásakor is létrehozhat a névteret. |
| **Event hub neve** | Az event hubs bemeneti adatokként neve. |
| **Event hub-házirend neve** | A megosztott elérési házirendet, amely hozzáférést biztosít az eseményközpontba. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. Ezzel a beállítással a rendszer automatikusan kitölti, kivéve, ha azt választja, adja meg az Eseményközpont-beállítások manuális.|
| **Event hub fogyasztói csoport** (ajánlott) | Erősen ajánlott minden Stream Analytics-feladat egy eltérő fogyasztói csoportot használnak. Ez a karakterlánc a fogyasztói csoportot kiolvasni az adatokat az event hubs segítségével azonosítja. Ha nem fogyasztói határoz meg csoportot, a Stream Analytics-feladat használ a $Default fogyasztói csoportot.  |
| **Partíciós kulcs** | Ha a bemenetet egy tulajdonság particionálja, akkor a tulajdonság nevét is hozzáadhatja. A partíciós kulcsok nem kötelezőek, és a lekérdezés teljesítményének növelésére szolgálnak, ha ezen a tulajdonságon a partíció vagy a GROUP BY záradék szerepel. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [other (protopuf, XML, tulajdonos...)](custom-deserializer.md)).  Győződjön meg arról, a JSON-formátumban a Specification igazítja, és nem tartalmazza a tizedes vezető 0. |
| **Kódolás** | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Esemény tömörítési típusa** | A tömörítési típus, például az egyik sem (alapértelmezett), vagy a GZip, Deflate a beérkező adatfolyam olvasására használják. |

Az adatokat egy Eseményközpont streambemenet származik, amikor hozzáfér a következő metaadatokat tartalmazó mezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** |A dátum és idő, az eseményt a Stream Analytics által feldolgozott. |
| **EventEnqueuedUtcTime** |A dátum és idő, az esemény Event hubs szolgáltatás által fogadott. |
| **PartitionId** |A bemeneti adapter nulla alapú Partícióazonosító. |

Ha például használja ezeket a mezőket, írhat egy lekérdezést a következő példához hasonlóan:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Ha IoT Hub útvonalakhoz végpontként használja az Event hub-t, a [GetMetadataPropertyValue függvénnyel](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)elérheti a IoT hub metaadatait.
> 

## <a name="stream-data-from-iot-hub"></a>Stream-adatokat az IoT hubról

Az Azure IoT Hub egy nagymértékben méretezhető közzétételi és előfizetési esemény, amely IoT forgatókönyvekre optimalizált.

A Stream Analytics IoT Hubból érkező események alapértelmezett időbélyege az az időbélyeg, amely az eseménynek a IoT Hub `EventEnqueuedUtcTime`. Ha az adatokat adatfolyamként szeretné feldolgozni az esemény hasznos adatait tartalmazó időbélyeget használva, az [időbélyeget kulcsszó szerint](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kell használnia.

### <a name="iot-hub-consumer-groups"></a>IOT hub fogyasztói csoportok

Be kell állítania minden Stream Analytics az IoT Hub bemeneti szeretné, hogy a saját fogyasztói csoportot. Ha egy feladat a önillesztést tartalmaz, vagy ha több bemenet rendelkezik, bemeneti adatokat olvashatják előfordulhat, hogy egynél több olvasó aktiválásához. Ebben a helyzetben befolyásolja az olvasók egyetlen felhasználói csoportban. Az Azure IoT Hub túllépő / partíciónként fogyasztói csoportot öt olvasók elkerüléséhez célszerű minden Stream Analytics-feladat egy fogyasztói csoportot kijelölje.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Állítsa be az IoT Hub egy bemeneti adatfolyam

Az alábbi táblázat a Azure Portal **új bemeneti** oldalának minden tulajdonságát ismerteti, amikor adatfolyam-bemenetként konfigurálja a IoT hub.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** | Egy rövid nevet, amely hivatkozik a bemeneti használatával a feladat lekérdezésben.|
| **Előfizetés** | Válassza ki az előfizetést, amelyben az IoT Hub erőforrás található. | 
| **IoT Hub** | Az IoT Hub bemeneti adatokként neve. |
| **Végpont** | Az IoT hub végpont.|
| **Megosztott elérési házirend neve** | A megosztott elérési házirendet, amely hozzáférést biztosít az IoT hubnak. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. |
| **Megosztott elérési házirend kulcsa** | A közös hozzáférési kulcs az IoT Hub elérésének engedélyezése.  Ez a beállítás automatikusan kitölti, kivéve, ha akkor választja, adja meg az Iot Hub beállításainak manuális. |
| **Fogyasztói csoport** | Azt javasoljuk, hogy minden Stream Analytics-feladat egy másik fogyasztói csoportot használjon. A fogyasztói csoportot kiolvasni az adatokat az IoT Hub segítségével. Stream Analytics a $Default fogyasztói csoportot használ, hacsak nem ad meg.  |
| **Partíciós kulcs** | Ha a bemenetet egy tulajdonság particionálja, akkor a tulajdonság nevét is hozzáadhatja. A partíciós kulcsok nem kötelezőek, és a lekérdezés teljesítményének növelésére szolgálnak, ha ezen a tulajdonságon a partíció vagy a GROUP BY záradék szerepel. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [other (protopuf, XML, tulajdonos...)](custom-deserializer.md)).  Győződjön meg arról, a JSON-formátumban a Specification igazítja, és nem tartalmazza a tizedes vezető 0. |
| **Kódolás** | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Esemény tömörítési típusa** | A tömörítési típus, például az egyik sem (alapértelmezett), vagy a GZip, Deflate a beérkező adatfolyam olvasására használják. |


Adatok streamelése az IoT Hub használata esetén elérhető lesz a következő metaadatokat tartalmazó mezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** | A dátum és idő, hogy az esemény feldolgozása. |
| **EventEnqueuedUtcTime** | A dátum és idő, az IoT Hub által fogadott esemény. |
| **PartitionId** | A bemeneti adapter nulla alapú Partícióazonosító. |
| **IoTHub. MessageId** | Vesse össze az IoT Hub kétirányú kommunikáció használt azonosító. |
| **IoTHub. CorrelationId** | Visszajelzés és az IoT Hub visszajelzés használt azonosító. |
| **IoTHub. ConnectionDeviceId** | Ez az üzenet elküldéséhez használt hitelesítési azonosítója. Ez az érték az IoT Hub által servicebound üzenetek van megjelölve. |
| **IoTHub. ConnectionDeviceGenerationId** | Ez az üzenet el lett küldve a hitelesített eszköz létrehozás-azonosítója. Ez az érték az IoT Hub által servicebound üzenetek van megjelölve. |
| **IoTHub. EnqueuedTime** | Az idő, amikor az az üzenetet az IoT Hub által fogadott volt. |


## <a name="stream-data-from-blob-storage"></a>Stream data Blob storage-ból
Az Azure Blob storage költséghatékony és méretezhető megoldást kínál a nagy mennyiségű strukturálatlan adatmennyiséget tárolni a felhőben tárolni forgatókönyvek esetén. A Blob storage szolgáltatásban tárolt adatok általában számít inaktív; adatok azonban Blobadatok adatok adatfolyamként által feldolgozható Stream Analytics. 

Naplófájl feldolgozása a Blob storage bemenetei között a Stream Analytics egy gyakran használt forgatókönyv. Ebben a forgatókönyvben a telemetriai adatok fájlok rendszerből rögzített, és elemzése és feldolgozása történhet az jelentéssel bíró adatokat nyerhet ki kell.

Stream Analytics a blob Storage-események alapértelmezett időbélyegzője a blob utolsó módosításának időbélyege, amely `BlobLastModifiedUtcTime`. Ha egy blobot a 13:00-as számú Storage-fiókba töltenek fel, és a Azure Stream Analytics-feladatot a 13:01 *-es* verzióval elindítják, akkor a blob nem lesz kiválasztva, mert a módosított idő a feladatok futtatási időszakán kívül esik.

Ha egy blobot a 13:00-es tárolóeszköz-tárolóba töltenek fel, és a Azure Stream Analytics-feladatot a 13:00-es vagy korábbi verzióban az *Egyéni idő* használatával indítja el, a rendszer a blobot fogja kiválasztani, mivel a módosítási idő a feladatok futási idején belül esik.

Ha a Azure Stream Analytics-feladatot *most már* a 13:00-es időpontban indítja el, és a rendszer feltölt egy blobot a 13:01-es számú Storage-fiók tárolójába, Azure stream Analytics fogja felvenni a blobot.

Ha az adatokat adatfolyamként szeretné feldolgozni az esemény hasznos adatait tartalmazó időbélyeget használva, az [időbélyeget kulcsszó szerint](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) kell használnia. Stream Analytics-feladat kér le adatokat az Azure Blob storage bemeneti másodpercenként blob fájl esetén érhető el. A blob-fájl nem érhető el, ha van egy exponenciális visszatartással 90 másodperc késéssel maximális ideje.

A CSV formátumú bemenetekhez szükség van egy fejlécre az adathalmaz mezőinek definiálásához, és az összes fejlécsor mezőnek egyedinek kell lennie.

> [!NOTE]
> Stream Analytics nem támogatja a meglévő blob fájl hozzáadása a tartalom. Stream Analytics tekinthetik meg a fájlok csak egyszer, és minden elvégzett módosításokat a fájlban után a feladat rendelkezik-e olvasási az adatok feldolgozása nem. Ajánlott eljárás, hogy egy blob fájlra vonatkozó összes adatot feltölteni, és ezután további újabb események felvétele egy másik, az új blob fájlt.

Ha nagyon nagy számú blobot tölt fel egyszerre, előfordulhat, hogy az Stream Analytics ritka esetekben kihagy néhány blob olvasását. Azt javasoljuk, hogy a blob Storage-on kívül legalább két másodpercen belül töltsön fel blobokat. Ha ez a beállítás nem valósítható meg, akkor a Event Hubs használatával nagy mennyiségű eseményt továbbíthat. 

### <a name="configure-blob-storage-as-a-stream-input"></a>A bemeneti stream konfigurálása a Blob storage 

A következő táblázat a Azure Portal **új bemeneti** oldalának egyes tulajdonságait ismerteti, amikor a blob Storage-t adatfolyam-bevitelként konfigurálja.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** | Egy rövid nevet, amely hivatkozik a bemeneti használatával a feladat lekérdezésben. |
| **Előfizetés** | Válassza ki az előfizetést, amelyben az IoT Hub erőforrás található. | 
| **Storage-fiók** | Hol találhatók a blob fájlokat a storage-fiók neve. |
| **Storage-fiók kulcsa** | A storage-fiókhoz társított titkos kulcs. Ez a beállítás automatikusan kitölti, kivéve, ha azt választja, adja meg a Blob storage beállításainak manuális. |
| **Tároló** | A tárolót a bemeneti BLOB. Tárolók biztosítják a Microsoft Azure Blob service-ben tárolt blobok logikai jellegű csoportosítását. Az Azure Blob storage szolgáltatáshoz feltölt egy blobot, ha meg kell adnia egy adott blob-tárolót. Kiválaszthatja a **meglévő tároló használata** lehetőséget, vagy létrehozhat újat, hogy új tárolót **hozzon** létre.|
| **Elérésiút-minta** (nem kötelező) | A fájl elérési útja, az a megadott tárolóban található blobok helyének azonosításához használt. Ha a tároló gyökeréből kívánja beolvasni a blobokat, ne állítson be elérésiút-mintát. Az elérési úton megadhatja a következő három változó egy vagy több példányát: `{date}`, `{time}`vagy `{partition}`<br/><br/>1\. példa: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>2\. példa: `cluster1/logs/{date}`<br/><br/>A `*` karakter nem engedélyezett érték az elérési út előtagja számára. Csak érvényes <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob-karakterek</a> engedélyezettek. Ne foglalja bele a tároló nevét vagy fájlnevét. |
| **Dátumformátum** (nem kötelező) | Ha a dátum változó az útvonalban, a dátumformátum, amelyben a fájlok vannak rendszerezve. Például: `YYYY/MM/DD` |
| **Idő formátuma** (nem kötelező) |  Ha a idő változó az elérési út, az időformátum, amelyben a fájlok vannak rendszerezve. Jelenleg az egyetlen támogatott érték `HH` óra. |
| **Partíciós kulcs** | Ha a bemenetet egy tulajdonság particionálja, akkor a tulajdonság nevét is hozzáadhatja. A partíciós kulcsok nem kötelezőek, és a lekérdezés teljesítményének növelésére szolgálnak, ha ezen a tulajdonságon a partíció vagy a GROUP BY záradék szerepel. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [other (protopuf, XML, tulajdonos...)](custom-deserializer.md)).  Győződjön meg arról, a JSON-formátumban a Specification igazítja, és nem tartalmazza a tizedes vezető 0. |
| **Kódolás** | A fürt megosztott kötetei szolgáltatás és a JSON az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Tömörítés** | A tömörítési típus, például az egyik sem (alapértelmezett), vagy a GZip, Deflate a beérkező adatfolyam olvasására használják. |

Az adatok egy Blob storage forrásból származik, amikor hozzáfér a következő metaadatokat tartalmazó mezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **BlobName** |Az esemény tartalomcsomagokból származó a bemeneti blob neve. |
| **EventProcessedUtcTime** |A dátum és idő, az eseményt a Stream Analytics által feldolgozott. |
| **BlobLastModifiedUtcTime** |A dátum és a blob utolsó módosításának időpontja. |
| **PartitionId** |A bemeneti adapter nulla alapú Partícióazonosító. |

Ha például használja ezeket a mezőket, írhat egy lekérdezést a következő példához hasonlóan:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Gyors útmutató: Stream Analytics-feladatok létrehozása a Azure Portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

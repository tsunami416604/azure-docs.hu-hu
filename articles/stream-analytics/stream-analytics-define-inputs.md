---
title: Adatok továbbítása bemenetként Azure Stream Analytics
description: További információ a Azure Stream Analytics adatkapcsolatainak beállításáról. A bemenetek között szerepelnek az események adatfolyamai, valamint az adatok is.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 52f333a8e39dfd8f68666e6438a7d40414b6f958
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83701418"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Adatok továbbítása bemenetként Stream Analytics

A Stream Analytics az Azure-adatstreamekkel való első osztályú integrációt három típusú erőforrásból származó bemenetként:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Ezek a bemeneti erőforrások ugyanabban az Azure-előfizetésben lehetnek, mint a Stream Analytics vagy egy másik előfizetés.

### <a name="compression"></a>Tömörítés

Stream Analytics támogatja a tömörítést az összes adatfolyam bemeneti forrásaként. A támogatott tömörítési típusok a következők: none, GZip és deflate Compression. A tömörítés támogatása nem érhető el a hivatkozási értékekhez. Ha a bemeneti formátum a tömörített adatok Avro, a rendszer transzparens módon kezeli az adatokat. Nem kell megadnia a Avro szerializálási tömörítési típust. 

## <a name="create-edit-or-test-inputs"></a>Bemenetek létrehozása, szerkesztése vagy tesztelése

A [Azure Portal](stream-analytics-quick-create-portal.md), a [Visual Studio](stream-analytics-quick-create-vs.md)és a [Visual Studio Code](quick-create-vs-code.md) használatával hozzáadhat és megtekintheti vagy szerkesztheti a meglévő bemeneteket a folyamatos átviteli feladatban. A Azure Portal, a [Visual Studio](stream-analytics-vs-tools-local-run.md)és a [Visual Studio Code](visual-studio-code-local-run.md)használatával is tesztelheti a bemeneti kapcsolatokat és tesztelheti a mintavételezési [lekérdezéseket](stream-analytics-manage-job.md#test-your-query) . A lekérdezés írásakor megjelenik a FROM záradékban szereplő bemenet. Az elérhető bemenetek listáját a portál **lekérdezés** lapján érheti el. Ha több bemenetet szeretne használni, `JOIN` több lekérdezést is írhat `SELECT` .


## <a name="stream-data-from-event-hubs"></a>Adatok streamelése az Event Hubsból

Az Azure Event Hubs a nagymértékben méretezhető közzétételi és előfizetési események betöltését teszi lehetővé. Az Event hub másodpercenként akár több millió eseményt is gyűjthet, így a csatlakoztatott eszközök és alkalmazások által előállított nagy mennyiségű adatot feldolgozhatja és elemezheti. Együtt Event Hubs és Stream Analytics teljes körű megoldást biztosít a valós idejű elemzésekhez. Event Hubs lehetővé teszi az események valós idejű betöltését az Azure-ba, és Stream Analytics a feladatok valós időben tudják feldolgozni ezeket az eseményeket. Küldhet például webes kattintásokat, szenzor-olvasási vagy online naplózási eseményeket Event Hubsba. Ezután létrehozhat Stream Analytics feladatokat, hogy a rendszer a valós idejű szűréshez, az összesítéshez és a korrelációhoz tartozó bemeneti adatfolyamként használja a Event Hubs.

`EventEnqueuedUtcTime`egy esemény központba való érkezésének időbélyegzője, és a Event Hubsról Stream Analyticsra érkező események alapértelmezett időbélyegzője. Ha az adatokat adatfolyamként szeretné feldolgozni az esemény hasznos adatait tartalmazó időbélyeget használva, az [időbélyeget kulcsszó szerint](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kell használnia.

### <a name="event-hubs-consumer-groups"></a>Event Hubs fogyasztói csoportok

Az egyes Stream Analytics Event hub-bemeneteket úgy kell konfigurálni, hogy saját fogyasztói csoporttal rendelkezzenek. Ha a feladatok önillesztést tartalmaznak, vagy több bemenettel rendelkeznek, előfordulhat, hogy egyes bemeneti adatokat több olvasó is olvashatja. Ez a helyzet befolyásolja az olvasók számát egyetlen fogyasztói csoportban. Ha el szeretné kerülni, hogy a felhasználónkénti csoportonként legfeljebb öt olvasó Event Hubs korlátozza az egyes Stream Analytics feladatokhoz tartozó fogyasztói csoportok kijelölését. A standard szintű Event hub esetében 20 fogyasztói csoport is engedélyezett. További információ: [Azure stream Analytics bemenetek hibáinak megoldása](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Bemenet létrehozása Event Hubsból

Az alábbi táblázat a Azure Portal **új bemeneti** lapján lévő összes tulajdonságot ismerteti egy Event hub adatbemenetének adatfolyamként történő továbbításához:

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** |A feladathoz tartozó lekérdezésben használt rövid név, amely erre a bemenetre hivatkozik. |
| **Előfizetés** | Válassza ki azt az előfizetést, amelyben az Event hub-erőforrás létezik. | 
| **Event Hubs-névtér** | Az Event hub-névtér üzenetküldési entitások készletének tárolója. Új Event hub létrehozásakor a rendszer létrehozza a névteret is. |
| **Event hub neve** | Az Event hub bemenetként használandó neve. |
| **Eseményközpont szabályzatának neve** | Az Event hub elérését biztosító közös hozzáférési szabályzat. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Ez a beállítás automatikusan ki van töltve, kivéve, ha az Event hub beállításainak manuális megadását választja.|
| **Event hub fogyasztói csoport** (ajánlott) | Kifejezetten ajánlott külön fogyasztói csoportot használni minden Stream Analytics feladathoz. Ez a karakterlánc azonosítja az Event hub adatainak betöltéséhez használandó fogyasztói csoportot. Ha nincs megadva fogyasztói csoport, a Stream Analytics-feladatokhoz a $Default fogyasztói csoportot használja.  |
| **Partíciókulcs** | Ha a bemenetet egy tulajdonság particionálja, akkor a tulajdonság nevét is hozzáadhatja. A partíciós kulcsok nem kötelezőek, és a lekérdezés teljesítményének növelésére szolgálnak, ha ezen a tulajdonságon a partíció vagy a GROUP BY záradék szerepel. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [other (protopuf, XML, tulajdonos...)](custom-deserializer.md)).  Győződjön meg arról, hogy a JSON formátum a specifikációhoz igazodik, és nem tartalmazza a kezdő 0 számjegyek számjegyeit. |
| **Encoding** | Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Esemény tömörítési típusa** | A bejövő adatfolyam olvasásához használt tömörítési típus, például none (alapértelmezett), GZip vagy deflating. |

Ha az adatok egy Event hub stream-bemenetből származnak, akkor a Stream Analytics lekérdezésben a következő metaadat-mezőkhöz férhet hozzá:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** |Az esemény Stream Analytics általi feldolgozásának dátuma és időpontja. |
| **EventEnqueuedUtcTime** |Az esemény Event Hubs általi fogadásának dátuma és időpontja. |
| **PartitionId** |A bemeneti adapter nulla alapú partíciójának azonosítója. |

A mezők használatával például a következő példához hasonló lekérdezést írhat:

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

## <a name="stream-data-from-iot-hub"></a>Stream-adatok IoT Hub

Az Azure IoT Hub egy nagymértékben méretezhető közzétételi és előfizetési esemény, amely IoT forgatókönyvekre optimalizált.

A Stream Analytics IoT Hubból érkező események alapértelmezett időbélyege az az időbélyeg, amely az eseménynek a IoT Hub érkezett `EventEnqueuedUtcTime` . Ha az adatokat adatfolyamként szeretné feldolgozni az esemény hasznos adatait tartalmazó időbélyeget használva, az [időbélyeget kulcsszó szerint](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kell használnia.

### <a name="iot-hub-consumer-groups"></a>IOT hub fogyasztói csoportok

Az egyes Stream Analytics IoT Hub bemeneteket úgy kell konfigurálni, hogy saját fogyasztói csoporttal rendelkezzenek. Ha a feladatok önillesztést tartalmaznak, vagy ha több bemenettel rendelkeznek, előfordulhat, hogy egy adott bemenetet több olvasó is olvas Ez a helyzet befolyásolja az olvasók számát egyetlen fogyasztói csoportban. Ha el szeretné kerülni, hogy az Azure IoT Hub a felhasználónkénti csoportonként legfeljebb öt olvasót adjon meg, az ajánlott eljárás az egyes Stream Analytics feladatokhoz tartozó fogyasztói csoportok kijelölése.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>IoT Hub konfigurálása adatfolyam-bemenetként

Az alábbi táblázat a Azure Portal **új bemeneti** oldalának minden tulajdonságát ismerteti, amikor adatfolyam-bemenetként konfigurálja a IoT hub.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** | A feladathoz tartozó lekérdezésben használt rövid név, amely erre a bemenetre hivatkozik.|
| **Előfizetés** | Válassza ki azt az előfizetést, amelyben a IoT Hub erőforrás létezik. | 
| **IoT Hub** | A bemenetként használandó IoT Hub neve. |
| **Végpont** | A IoT Hub végpontja.|
| **Megosztott hozzáférési szabályzat neve** | A IoT Hub elérését biztosító közös hozzáférési szabályzat. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
| **Megosztott elérési házirend kulcsa** | A IoT Hub elérésének engedélyezéséhez használt megosztott elérési kulcs.  Ezt a beállítást automatikusan kitölti a rendszer, hacsak nem kiválasztja a IOT hub beállításainak manuális megadására szolgáló lehetőséget. |
| **Fogyasztói csoport** | Javasoljuk, hogy minden Stream Analytics feladatokhoz használjon egy másik fogyasztói csoportot. A fogyasztói csoport a IoT Hub adatainak betöltésére szolgál. Stream Analytics a $Default fogyasztói csoportot használja, hacsak nem ad meg mást.  |
| **Partíciókulcs** | Ha a bemenetet egy tulajdonság particionálja, akkor a tulajdonság nevét is hozzáadhatja. A partíciós kulcsok nem kötelezőek, és a lekérdezés teljesítményének növelésére szolgálnak, ha ezen a tulajdonságon a partíció vagy a GROUP BY záradék szerepel. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [other (protopuf, XML, tulajdonos...)](custom-deserializer.md)).  Győződjön meg arról, hogy a JSON formátum a specifikációhoz igazodik, és nem tartalmazza a kezdő 0 számjegyek számjegyeit. |
| **Encoding** | Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Esemény tömörítési típusa** | A bejövő adatfolyam olvasásához használt tömörítési típus, például none (alapértelmezett), GZip vagy deflating. |


Ha IoT Hub stream-adatokat használ, a Stream Analytics lekérdezésben a következő metaadat-mezőkhöz férhet hozzá:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** | Az esemény feldolgozásának dátuma és időpontja. |
| **EventEnqueuedUtcTime** | Az a dátum és időpont, amikor a IoT Hub megkapta az eseményt. |
| **PartitionId** | A bemeneti adapter nulla alapú partíciójának azonosítója. |
| **IoTHub. MessageId** | Egy azonosító, amely a IoT Hub kétirányú kommunikációjának összekapcsolására szolgál. |
| **IoTHub. CorrelationId** | Egy azonosító, amelyet a rendszer az IoT Hubban lévő üzenetekre adott válaszokban és visszajelzésekben használ. |
| **IoTHub. ConnectionDeviceId** | Az üzenet elküldéséhez használt hitelesítési azonosító. Ez az érték a IoT Hub servicebound-üzeneteire van pecsételve. |
| **IoTHub. ConnectionDeviceGenerationId** | Az üzenet elküldéséhez használt hitelesített eszköz létrehozási azonosítója. Ez az érték a IoT Hub servicebound-üzeneteire van pecsételve. |
| **IoTHub. EnqueuedTime** | Az az idő, amikor a IoT Hub fogadta az üzenetet. |


## <a name="stream-data-from-blob-storage"></a>Stream-adatok a blob Storage-ból
Az Azure Blob Storage költséghatékony és méretezhető megoldást kínál olyan forgatókönyvek esetén, amelyekben nagy mennyiségű strukturálatlan adat tárolható a felhőben. A blob Storage-ban tárolt adatok általában a nyugalmi állapotban lévő adatoknak minősülnek. a blob-adatstreamek azonban Stream Analytics alapján is feldolgozhatók adatfolyamként. 

A log Processing a blob Storage-bemenetek Stream Analytics használatával történő használatának leggyakrabban használt forgatókönyve. Ebben a forgatókönyvben a telemetria adatfájljait a rendszer rögzíti, és elemezni és feldolgozni kell az értelmes adatok kinyerése érdekében.

Stream Analytics a blob Storage-események alapértelmezett időbélyegzője a blob utolsó módosításának időbélyegzője `BlobLastModifiedUtcTime` . Ha egy blobot a 13:00-as számú Storage-fiókba töltenek fel, és a Azure Stream Analytics-feladatot a 13:01 *-es* verzióval elindítják, akkor a blob nem lesz kiválasztva, mert a módosított idő a feladatok futtatási időszakán kívül esik.

Ha egy blobot a 13:00-es tárolóeszköz-tárolóba töltenek fel, és a Azure Stream Analytics-feladatot a 13:00-es vagy korábbi verzióban az *Egyéni idő* használatával indítja el, a rendszer a blobot fogja kiválasztani, mivel a módosítási idő a feladatok futási idején belül esik.

Ha a Azure Stream Analytics-feladatot *most már* a 13:00-es időpontban indítja el, és a rendszer feltölt egy blobot a 13:01-es számú Storage-fiók tárolójába, Azure stream Analytics fogja felvenni a blobot. Az egyes blobokhoz rendelt időbélyeg csak a rendszeren alapul `BlobLastModifiedTime` . Az a mappa, amelyben a blob található, nem kapcsolódik a hozzárendelt időbélyeghez. Ha például egy 2019-11-11-es blob *2019/10-01/00/b1.txt* van `BlobLastModifiedTime` , akkor az ehhez a blobhoz rendelt időbélyeg az 2019-11-11.

Ha az adatokat adatfolyamként szeretné feldolgozni az esemény hasznos adatait tartalmazó időbélyeget használva, az [időbélyeget kulcsszó szerint](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) kell használnia. Egy Stream Analytics feladat minden másodpercben lekéri az adatokat az Azure Blob Storage-ból, ha a blob-fájl elérhető. Ha a blob-fájl nem érhető el, a 90 másodperces maximális késleltetésű exponenciális leállítási van.

A CSV formátumú bemenetekhez szükség van egy fejlécre az adathalmaz mezőinek definiálásához, és az összes fejlécsor mezőnek egyedinek kell lennie.

> [!NOTE]
> A Stream Analytics nem támogatja tartalom hozzáadását egy meglévő blob-fájlhoz. A Stream Analytics csak egyszer fogja megtekinteni az egyes fájlokat, és a fájlban a feladatok beolvasása után végrehajtott módosítások nem lesznek feldolgozva. Az ajánlott eljárás az, hogy egy blob-fájl összes fájlját egyszerre töltse fel, majd további újabb eseményeket adjon hozzá egy másik új blob-fájlhoz.

Azokban az esetekben, amelyekben számos blobot folyamatosan felvesznek, és Stream Analytics a Blobok feldolgozását a hozzáadásuk során, előfordulhat, hogy egyes Blobok kihagyása ritka esetekben történik a részletessége miatt `BlobLastModifiedTime` . Ezt a Blobok legalább két másodpercen belül történő feltöltésével csökkentheti. Ha ez a beállítás nem valósítható meg, akkor a Event Hubs használatával nagy mennyiségű eseményt továbbíthat.

### <a name="configure-blob-storage-as-a-stream-input"></a>BLOB Storage beállítása stream-bemenetként 

A következő táblázat a Azure Portal **új bemeneti** oldalának egyes tulajdonságait ismerteti, amikor a blob Storage-t adatfolyam-bevitelként konfigurálja.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** | A feladathoz tartozó lekérdezésben használt rövid név, amely erre a bemenetre hivatkozik. |
| **Előfizetés** | Válassza ki azt az előfizetést, amelyben a IoT Hub erőforrás létezik. | 
| **Storage-fiók** | Annak a Storage-fióknak a neve, ahol a blob-fájlok találhatók. |
| **Storage-fiók kulcsa** | A Storage-fiókhoz társított titkos kulcs. Ezt a beállítást automatikusan kitölti a rendszer, hacsak nem kiválasztja a blob Storage-beállítások manuális megadásának lehetőségét. |
| **Tároló** | A blob bemenetének tárolója. A tárolók logikai csoportosítást biztosítanak a Microsoft Azure Blob service tárolt blobokhoz. Amikor feltölt egy blobot az Azure Blob Storage szolgáltatásba, meg kell adnia egy tárolót a blobhoz. Kiválaszthatja a **meglévő tároló használata** lehetőséget, vagy létrehozhat újat, hogy új tárolót **hozzon** létre.|
| **Elérésiút-minta** (nem kötelező) | A megadott tárolóban található Blobok megkereséséhez használt fájl elérési útja. Ha a tároló gyökeréből kívánja beolvasni a blobokat, ne állítson be elérésiút-mintát. Az elérési úton megadhatja a következő három változó egy vagy több példányát: `{date}` , `{time}` vagy`{partition}`<br/><br/>1. példa:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>2. példa:`cluster1/logs/{date}`<br/><br/>A `*` karakter nem engedélyezett érték az elérési út előtagja számára. Csak érvényes <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob-karakterek</a> engedélyezettek. Ne foglalja bele a tároló nevét vagy fájlnevét. |
| **Dátumformátum** (nem kötelező) | Ha az elérési úton a Date változót használja, akkor a fájlok rendszerezésének dátumformátum. Például: `YYYY/MM/DD` <br/><br/> Ha a blob bemenete `{date}` vagy annak `{time}` elérési útja, a mappák növekvő sorrendben lesznek megvizsgálva.|
| **Idő formátuma** (nem kötelező) |  Ha az elérési úton a Time változót használja, akkor a fájlok rendszerezésének időformátuma. Jelenleg az egyetlen támogatott érték `HH` óra. |
| **Partíciókulcs** | Ha a bemenetet egy tulajdonság particionálja, akkor a tulajdonság nevét is hozzáadhatja. A partíciós kulcsok nem kötelezőek, és a lekérdezés teljesítményének növelésére szolgálnak, ha ezen a tulajdonságon a partíció vagy a GROUP BY záradék szerepel. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [other (protopuf, XML, tulajdonos...)](custom-deserializer.md)).  Győződjön meg arról, hogy a JSON formátum a specifikációhoz igazodik, és nem tartalmazza a kezdő 0 számjegyek számjegyeit. |
| **Encoding** | A CSV és a JSON esetében az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Tömörítés** | A bejövő adatfolyam olvasásához használt tömörítési típus, például none (alapértelmezett), GZip vagy deflating. |

Ha az adatok blob Storage-forrásból származnak, a Stream Analytics lekérdezésben a következő metaadatokat tartalmazó mezőkhöz férhet hozzá:

| Tulajdonság | Leírás |
| --- | --- |
| **BlobName** |Annak a bemeneti blobnak a neve, amelyből az esemény származik. |
| **EventProcessedUtcTime** |Az esemény Stream Analytics általi feldolgozásának dátuma és időpontja. |
| **BlobLastModifiedUtcTime** |A blob utolsó módosításának dátuma és időpontja. |
| **PartitionId** |A bemeneti adapter nulla alapú partíciójának azonosítója. |

A mezők használatával például a következő példához hasonló lekérdezést írhat:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

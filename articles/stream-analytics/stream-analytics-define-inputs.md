---
title: Adatok streamelése az Azure Stream Analytics szolgáltatásba való bevitelként
description: Ismerje meg az adatkapcsolat beállítását az Azure Stream Analytics szolgáltatásban. A bemenetek tartalmaznak egy adatfolyamot az eseményekből, valamint referenciaadatokat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254468"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Adatok streamelése a Stream Analytics szolgáltatásba való bevitelként

A Stream Analytics első osztályú integrációt kínál az Azure-adatfolyamokkal, mint háromféle erőforrás bemenetét:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob-tárhely](https://azure.microsoft.com/services/storage/blobs/) 

Ezek a bemeneti erőforrások ugyanabban az Azure-előfizetésben élhetnek, mint a Stream Analytics-feladat vagy egy másik előfizetés.

### <a name="compression"></a>Tömörítés

A Stream Analytics támogatja a tömörítést az összes adatfolyam-bemeneti forrás között. A támogatott tömörítési típusok a következők: Nincs, GZip és Deflate tömörítés. A hivatkozási adatokhoz nem áll rendelkezésre a tömörítés támogatása. Ha a bemeneti formátum Avro adatok tömörített, akkor a kezelt transzparens módon. Nem kell megadnia a tömörítés típusát Avro szerializálással. 

## <a name="create-edit-or-test-inputs"></a>Bemenetek létrehozása, szerkesztése és tesztelése

Az Azure [Portal](stream-analytics-quick-create-portal.md), a [Visual Studio](stream-analytics-quick-create-vs.md)és a [Visual Studio-kód](quick-create-vs-code.md) használatával meglévő bemeneteket adhat hozzá és tekinthet meg és szerkeszthet a streamelési feladatban. Az Azure Portal, a [Visual Studio](stream-analytics-vs-tools-local-run.md)és a Visual Studio [Code](visual-studio-code-local-run.md)mintaadataiból is tesztelheti a bemeneti kapcsolatokat és a [lekérdezéseket.](stream-analytics-manage-job.md#test-your-query) Lekérdezés írásakor a BEMENETet a FROM záradékban soroljuk fel. Az elérhető bemenetek listáját a portál **Lekérdezés** lapjáról szerezheti be. Ha több bemenetet szeretne használni, `JOIN` akkor több `SELECT` lekérdezést is írhat, vagy írhat több lekérdezést.


## <a name="stream-data-from-event-hubs"></a>Adatok streamelése az Event Hubsból

Az Azure Event Hubs nagymértékben méretezhető közzétételi és előfizetési eseménybetöltéseket biztosít. Egy eseményközpont másodpercenként több millió eseményt gyűjthet, így feldolgozhatja és elemezheti a csatlakoztatott eszközök és alkalmazások által előállított nagy mennyiségű adatot. Az Event Hubs és a Stream Analytics együttesen teljes körű megoldást kínál a valós idejű elemzésekhez. Az Event Hubs lehetővé teszi, hogy valós időben továbbítsa az eseményeket az Azure-ba, és a Stream Analytics-feladatok valós időben tudják feldolgozni ezeket az eseményeket. Például küldhet webes kattintásokat, érzékelőleolvasásokat vagy online naplóeseményeket az Event Hubs-nak. Ezután streamanalytics-feladatokat hozhat létre, amelyek segítségével az Event Hubs a bemeneti adatfolyamok valós idejű szűrés, összesítés és korreláció.

`EventEnqueuedUtcTime`egy esemény eseményközpontba érkezésének időbélyege, és az Event Hubs-ból a Stream Analytics-be érkező események alapértelmezett időbélyege. Az adatok adatfolyamként történő feldolgozásához az esemény hasznos adatában időbélyeg használatával kell használnia a [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kulcsszót.

### <a name="event-hubs-consumer-groups"></a>Eseményközpontok fogyasztói csoportok

Minden Stream Analytics-eseményközpont-bemenetet saját fogyasztói csoporttal kell konfigurálni. Ha egy feladat önillesztést tartalmaz, vagy több bemenettel rendelkezik, előfordulhat, hogy egyes bemeneteket egynél több olvasó olvas lefelé. Ez a helyzet hatással van az olvasók egy fogyasztói csoportban. Annak elkerülése érdekében, hogy túllépje az Event Hubs korlátöt, amely partíciónként és fogyasztói csoportonként öt olvasóra van, ajánlott egy fogyasztói csoportot kijelölni minden egyes Stream Analytics-feladathoz. A standard szintű eseményközponthoz legfeljebb 20 fogyasztói csoport van. További információt az [Azure Stream Analytics-bemenetek hibaelhárítása című témakörben talál.](stream-analytics-troubleshoot-input.md)

### <a name="create-an-input-from-event-hubs"></a>Bemenet létrehozása az Eseményközpontokból

Az alábbi táblázat ismerteti az egyes tulajdonok az **Új beviteli** lap az Azure Portalon adatbevitelt egy eseményközpontból:

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** |A feladat lekérdezésében használt rövid név, amely erre a bemenetre hivatkozik. |
| **Előfizetés** | Válassza ki azt az előfizetést, amelyben az Event hub erőforrás létezik. | 
| **Event Hubs-névtér** | Az Event Hub névtér egy tároló egy sor üzenetküldő entitások. Új eseményközpont létrehozásakor a névteret is létre kell hoznia. |
| **Az eseményközpont neve** | A bemenetként használandó eseményközpont neve. |
| **Eseményközpont szabályzatának neve** | A megosztott hozzáférési szabályzat, amely hozzáférést biztosít az Event Hub. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. Ez a beállítás automatikusan kitöltődik, kivéve, ha az Event Hub beállításainak manuális megadását választja.|
| **Az Event Hub fogyasztói csoportja** (ajánlott) | Erősen ajánlott minden egyes Stream Analytics-feladathoz külön fogyasztói csoportot használni. Ez a karakterlánc azonosítja a fogyasztói csoportot, amelyet az eseményközpontból származó adatok betöltéséhez használnak. Ha nincs megadva fogyasztói csoport, a Stream Analytics-feladat a $Default fogyasztói csoportot használja.  |
| **Partíciókulcs** | Ha a bemenetet egy tulajdonság particionálja, hozzáadhatja a tulajdonság nevét. A partíciókulcsok megadása nem kötelező, és a lekérdezés teljesítményének javítására szolgálnak, ha ezen a tulajdonságon partition by vagy GROUP BY záradék található. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [Egyéb (Protobuf, XML, védett...)](custom-deserializer.md).  Győződjön meg arról, hogy a JSON formátum igazodik a specifikációhoz, és nem tartalmazza a kezdő 0-t a decimális számokhoz. |
| **Kódolás** | Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Eseménytömörítés típusa** | A bejövő adatfolyam olvasásához használt tömörítési típus, például Nincs (alapértelmezett), GZip vagy Deflate. |

Ha az adatok egy Event Hub-adatfolyam-bemenetből származnak, a Stream Analytics-lekérdezésben a következő metaadatmezőkhöz férhet hozzá:

| Tulajdonság | Leírás |
| --- | --- |
| **EseményprocessedUtcTime** |Az esemény Stream Analytics általi feldolgozásának dátuma és időpontja. |
| **EventEnqueuedUtcTime** |Az esemény eseményközpontok általi fogadásának dátuma és időpontja. |
| **PartitionId** |A bemeneti adapter nulla alapú partícióazonosítója. |

Ezekkel a mezőkkel például a következő példához hasonló lekérdezést írhat:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Ha az Event Hubot használja az IoT Hub-útvonalak végpontjaként, a [GetMetadataPropertyValue függvény](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)használatával elérheti az IoT Hub metaadatait.
> 

## <a name="stream-data-from-iot-hub"></a>Adatok streamelése az IoT Hubról

Az Azure IoT Hub egy jól méretezhető közzétételi-előfizetési eseménybetöltési optimalizált IoT-forgatókönyvek.

A Stream Analytics IoT Hubból érkező események alapértelmezett időbélyege az az időbélyeg, amely `EventEnqueuedUtcTime`az esemény az IoT Hubba érkezett, amely. Az adatok adatfolyamként történő feldolgozásához az esemény hasznos adatában időbélyeg használatával kell használnia a [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kulcsszót.

### <a name="iot-hub-consumer-groups"></a>Iot Hub fogyasztói csoportok

Minden Stream Analytics IoT Hub-bemenetet saját fogyasztói csoporttal kell konfigurálnia. Ha egy feladat önillesztést tartalmaz, vagy ha több bemenettel rendelkezik, előfordulhat, hogy egynél több olvasó olvas be egy bemenetet. Ez a helyzet hatással van az olvasók egy fogyasztói csoportban. Annak elkerülése érdekében, hogy túllépje az Azure IoT Hub korlátöt, amely partíciónként és fogyasztói csoportonként öt olvasóra van, ajánlott egy fogyasztói csoportot kijelölni minden egyes Stream Analytics-feladathoz.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>IoT-központ konfigurálása adatfolyam-bemenetként

Az alábbi táblázat ismerteti az egyes tulajdonok az **Új beviteli** lap az Azure Portalon, amikor konfigurálja az IoT Hub adatfolyam-bemenetként.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** | A feladat lekérdezésében használt rövid név, amely erre a bemenetre hivatkozik.|
| **Előfizetés** | Válassza ki azt az előfizetést, amelyben az IoT Hub-erőforrás létezik. | 
| **IoT Hub** | A bemenetként használandó IoT Hub neve. |
| **Végpont** | Az IoT Hub végpontja.|
| **Megosztott hozzáférési szabályzat neve** | A megosztott hozzáférési szabályzat, amely hozzáférést biztosít az IoT Hubhoz. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. |
| **Megosztott hozzáférésházirend-kulcs** | Az IoT Hubhoz való hozzáférés engedélyezéséhez használt megosztott hozzáférési kulcs.  Ez a beállítás automatikusan kitöltődik, hacsak nem választja ki az Iot Hub beállításainak manuális megadását. |
| **Fogyasztói csoport** | Erősen ajánlott, hogy minden Stream Analytics-feladathoz használjon egy másik fogyasztói csoportot. A fogyasztói csoport az IoT Hubból származó adatok betöltéséhez használatos. A Stream Analytics a $Default fogyasztói csoportot használja, hacsak másként nem rendelkezik.  |
| **Partíciókulcs** | Ha a bemenetet egy tulajdonság particionálja, hozzáadhatja a tulajdonság nevét. A partíciókulcsok megadása nem kötelező, és a lekérdezés teljesítményének javítására szolgálnak, ha ezen a tulajdonságon partition by vagy GROUP BY záradék található. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [Egyéb (Protobuf, XML, védett...)](custom-deserializer.md).  Győződjön meg arról, hogy a JSON formátum igazodik a specifikációhoz, és nem tartalmazza a kezdő 0-t a decimális számokhoz. |
| **Kódolás** | Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Eseménytömörítés típusa** | A bejövő adatfolyam olvasásához használt tömörítési típus, például Nincs (alapértelmezett), GZip vagy Deflate. |


Ha egy IoT Hubról származó adatfolyam-adatokat használ, a Stream Analytics-lekérdezésben a következő metaadatmezőkhöz férhet hozzá:

| Tulajdonság | Leírás |
| --- | --- |
| **EseményprocessedUtcTime** | Az esemény feldolgozásának dátuma és időpontja. |
| **EventEnqueuedUtcTime** | Az esemény IoT Hub általi fogadásának dátuma és időpontja. |
| **PartitionId** | A bemeneti adapter nulla alapú partícióazonosítója. |
| **IoTHub.MessageId** | Egy azonosító, amely az IoT Hub kétirányú kommunikációjának korrelációjára szolgál. |
| **IoTHub.CorrelationId** | Az IoT Hub üzenetválaszaiban és visszajelzéseiben használt azonosító. |
| **IoTHub.ConnectionDeviceId** | Az üzenet küldéséhez használt hitelesítési azonosító. Ezt az értéket az IoT Hub a szolgáltatáshoz kötött üzenetekre pecsételi. |
| **IoTHub.ConnectionDeviceGenerationId** | Az üzenet küldéséhez használt hitelesített eszköz generációs azonosítója. Ezt az értéket az IoT Hub a szolgáltatáshoz kötött üzenetekre pecsételi. |
| **IoTHub.EnqueuedTime** | Az az időpont, amikor az ioT Hub megkapta az üzenetet. |


## <a name="stream-data-from-blob-storage"></a>Adatok streamelése a Blob storage-ból
A felhőben tárolandó nagy mennyiségű strukturálatlan adatokkal rendelkező forgatókönyvek esetében az Azure Blob storage költséghatékony és méretezhető megoldást kínál. A Blob storage-ban lévő adatok általában nyugalmi adatoknak számítanak; a blobadatok at azonban adatfolyamként is feldolgozhatja a Stream Analytics. 

A naplófeldolgozás egy általánosan használt forgatókönyv a Blob storage-bemenetek streamanalytics használatával. Ebben a forgatókönyvben a telemetriai adatfájlok at rögzítették a rendszerből, és elemezni kell, és fel kell dolgozni értelmes adatok kinyeréséhez.

A Blob storage-események alapértelmezett időbélyege a Stream Analytics szolgáltatásban az `BlobLastModifiedUtcTime`az időbélyeg, amelyet a blob utoljára módosított, azaz a. Ha egy blob feltöltése egy tárfiókba 13:00-kor, és az Azure Stream Analytics-feladat elindul a lehetőség *most* 13:01, a blob nem lesz felvette, mivel a módosított idő kívül esik a feladat futási időszak.

Ha egy blob ot 13:00-kor feltölt egy tárfiók-tárolóba, és az Azure Stream Analytics-feladat 13:00-kor vagy korábban kezdi el az *egyéni idő* használatát, a blob ot felveszi, mivel a módosított idő a feladat futási időszakába esik.

Ha egy Azure Stream Analytics-feladat 13:00-kor kezdi el a *most,* és 13:01-kor feltölt egy blobot a tárfiók tárolóba, az Azure Stream Analytics felveszi a blobot.

Az adatok adatfolyamként történő feldolgozásához az esemény hasznos adatában időbélyeg használatával kell használnia a [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) kulcsszót. A Stream Analytics-feladat lekéri az adatokat az Azure Blob storage bemeneti másodpercenként, ha a blob fájl érhető el. Ha a blobfájl nem érhető el, akkor egy exponenciális visszalépés, amelynek maximális késleltetése 90 másodperc.

A CSV-formátumú bemenetek esetében fejlécsor szükséges az adatkészlet mezőinek meghatározásához, és minden fejlécsormezőnek egyedinek kell lennie.

> [!NOTE]
> A Stream Analytics nem támogatja a tartalom hozzáadását egy meglévő blobfájlhoz. A Stream Analytics csak egyszer tekinti meg az egyes fájlokat, és a feladat olvasása után a fájlban bekövetkező módosításokat a rendszer nem dolgozza fel. Ajánlott eljárás ként töltse fel egy blobfájl összes adatát egyszerre, majd adjon hozzá további újabb eseményeket egy másik, új blobfájlhoz.

Ha egyszerre nagyon nagy számú blobot tölt fel, előfordulhat, hogy a Stream Analytics ritka esetekben néhány blob olvasását kihagyja. Javasoljuk, hogy a blobok legalább 2 másodperc különbséggel blobstorage feltöltése. Ha ez a beállítás nem valósítható meg, az Event Hubs használatával nagy mennyiségű eseményt streamelhet. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Blob-tároló konfigurálása adatfolyam-bemenetként 

Az alábbi táblázat ismerteti az egyes tulajdonok az **Új beviteli** lap az Azure Portalon, amikor konfigurálja a Blob storage adatfolyam-bemenetként.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti alias** | A feladat lekérdezésében használt rövid név, amely erre a bemenetre hivatkozik. |
| **Előfizetés** | Válassza ki azt az előfizetést, amelyben az IoT Hub-erőforrás létezik. | 
| **Tárfiók** | Annak a tárfióknak a neve, ahol a blobfájlok találhatók. |
| **Tárfiók kulcsa** | A tárfiókhoz társított titkos kulcs. Ez a beállítás automatikusan kitöltődik, kivéve, ha a Blob storage-beállítások manuális megadására vonatkozó lehetőséget választja. |
| **Konténer** | A blob bemeneti tárolója. A tárolók logikai csoportosítást biztosítanak a Microsoft Azure Blob szolgáltatásban tárolt blobok számára. Amikor feltölt egy blobot az Azure Blob storage szolgáltatásba, meg kell adnia egy tárolót az adott blobhoz. Választhat, vagy **használja a meglévő** tárolót, vagy **hozzon létre új** tárolót.|
| **Görbeminta** (nem kötelező) | A megadott tárolón belüli blobok megkereséséhez használt fájlelérési út. Ha a tároló gyökeréből szeretné olvasni a blobokat, ne állítson be elérési útmintát. Az elérési úton a következő három változó egy vagy több `{date}` `{time}`példányát adhatja meg: , vagy`{partition}`<br/><br/>1. példa:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>2. példa:`cluster1/logs/{date}`<br/><br/>A `*` karakter nem engedélyezett érték az elérési út előtagja számára. Csak érvényes <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob karakterek</a> engedélyezettek. Ne adjon meg tárolóneveket vagy fájlneveket. |
| **Dátumformátum** (nem kötelező) | Ha a dátumváltozót használja az elérési úton, akkor a fájlok rendszerezésének dátumformátuma. Például: `YYYY/MM/DD` |
| **Időformátum** (nem kötelező) |  Ha az elérési útban az időváltozót használja, akkor a fájlok rendszerezésének időformátuma. Jelenleg az egyetlen `HH` támogatott érték órákra szól. |
| **Partíciókulcs** | Ha a bemenetet egy tulajdonság particionálja, hozzáadhatja a tulajdonság nevét. A partíciókulcsok megadása nem kötelező, és a lekérdezés teljesítményének javítására szolgálnak, ha ezen a tulajdonságon partition by vagy GROUP BY záradék található. |
| **Eseményszerializációs formátum** | A bejövő adatfolyam szerializálási formátuma (JSON, CSV, Avro vagy [Egyéb (Protobuf, XML, védett...)](custom-deserializer.md).  Győződjön meg arról, hogy a JSON formátum igazodik a specifikációhoz, és nem tartalmazza a kezdő 0-t a decimális számokhoz. |
| **Kódolás** | A CSV és a JSON esetében jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Tömörítés** | A bejövő adatfolyam olvasásához használt tömörítési típus, például Nincs (alapértelmezett), GZip vagy Deflate. |

Ha az adatok Blob-tárforrásból származnak, a Stream Analytics-lekérdezésben a következő metaadatmezőkhöz férhet hozzá:

| Tulajdonság | Leírás |
| --- | --- |
| **BlobName (BlobName)** |Annak a bemeneti blobnak a neve, amelyből az esemény származik. |
| **EseményprocessedUtcTime** |Az esemény Stream Analytics általi feldolgozásának dátuma és időpontja. |
| **BlobLastModifiedUtcTime** |A blob utolsó módosításának dátuma és időpontja. |
| **PartitionId** |A bemeneti adapter nulla alapú partícióazonosítója. |

Ezekkel a mezőkkel például a következő példához hasonló lekérdezést írhat:

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

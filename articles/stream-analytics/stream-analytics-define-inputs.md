---
title: Azure Stream Analytics a bemenő Stream az adatok
description: Ismerje meg az Azure Stream Analyticsben adatkapcsolat beállítása. Bemeneti események származó adatok adatfolyamot, és referenciaadatok is.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 9b75db835b0bdcc18cb82f427250630fb936d817
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341215"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream data bemeneti Stream analyticsbe

Stream Analytics bemenetként három típusú erőforrásokat az Azure data-adatfolyamok első osztályú integrálva van:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Ezen a bemeneti források is élő Azure-előfizetéshez, a Stream Analytics-feladat vagy egy másik előfizetésben található.

### <a name="compression"></a>Tömörítés
Stream Analytics támogatja a tömörítést minden stream bemeneti adatforrás esetében. Jelenleg támogatott referencia típusok a következők: None, GZip, és a Deflate tömörítést. A tömörítés támogatása nem érhető el a referenciaadatoknál. Ha a bemeneti formátum az Avro-adatok tömörített, azt transzparens módon történik. Adja meg a tömörítési típus szerializálása az Avro-nincs szükségünk. 

## <a name="create-edit-or-test-inputs"></a>Létrehozásához, szerkesztéséhez vagy bemenet tesztelése
Használhatja a [az Azure portal](https://portal.azure.com) való [hozzon létre új bemenetei között meg](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal#configure-input-to-the-job) és megtekintése vagy szerkesztése a folyamatos átviteli feladat a meglévő bemenetei között. A bemeneti kapcsolatok is tesztelheti és [tesztlekérdezések](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-manage-job#test-your-query) a mintaadatokat. Ha egy lekérdezést ír, a FROM záradékban található a bemeneti listázza. Megtekintheti az elérhető bemenetek listája a **lekérdezés** lapot a portálon. Ha több bemenet használni kívánt, `JOIN` őket vagy több írási `SELECT` lekérdezéseket.


## <a name="stream-data-from-event-hubs"></a>Adatok streamelése az Event Hubsból

Az Azure Event Hubs nyújt hatékonyan méretezhető közzétételi és előfizetési esemény ingestors. Egy eseményközpontba, másodpercenként több millió képes összegyűjteni, így egyszerűen és a csatlakoztatott eszközök és alkalmazások által létrehozott hatalmas adatmennyiségek elemzését. Együtt, az Event Hubs és a Stream Analytics egy teljes körű megoldást biztosítanak valós idejű elemzés. Az Event Hubs lehetővé teszi az Azure-ba való hírcsatorna-esemény valós idejű, és a Stream Analytics-feladatok valós idejű események feldolgozására képes. Például elküldheti webes kattintással, érzékelőinek vagy online alkalmazásnapló-események az Event hubs szolgáltatásba. Ezután létrehozhat Stream Analytics-feladatok a bemeneti adatok Streamek valós idejű szűrés, összesítenie és korrelációs az Event Hubs adatokként.

`EventEnqueuedUtcTime` egy esemény érkezési az eseményközpontok felé történő küldés időbélyegzője és a Stream Analytics az Event hubs Eseményközpontokból érkező események alapértelmezett időbélyegzője. Az adatok feldolgozása adatfolyamként időbélyeg használatával, abban az esetben, ha adattartalom kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszót.

### <a name="consumer-groups"></a>Felhasználói csoportok
Be kell állítania minden Stream Analytics eseményközpont szeretné, hogy a saját fogyasztói csoportot adjon meg. Amikor egy feladat önillesztést tartalmaz, vagy rendelkezik több bemenet, néhány bemenetei között meg egynél több olvasó aktiválásához előfordulhat, hogy olvashatják. Ebben a helyzetben befolyásolja az olvasók egyetlen felhasználói csoportban. Az Event Hubs túllépő / partíciónként fogyasztói csoportot öt olvasók elkerüléséhez célszerű minden Stream Analytics-feladat egy fogyasztói csoportot kijelölje. Emellett van egy legfeljebb 20 felhasználói csoportot egy eseményközpontba. További információkért lásd: [hibaelhárítása az Azure Stream Analytics bemenetek](stream-analytics-troubleshoot-input.md).

### <a name="stream-data-from-event-hubs"></a>Adatok streamelése az Event Hubsból
A következő táblázat ismerteti az egyes tulajdonságait a **új bemenet** lap egy eseményközpontból érkező bemeneti stream adatokhoz az Azure Portalon:

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti áljel** |Egy rövid nevet, amely hivatkozik a bemeneti használatával a feladat lekérdezésben. |
| **Előfizetés** | Válassza ki az előfizetést, amelyben az Event hub-erőforrás található. | 
| **Eseményközpont-névtér** | Az Event Hubs-névtér az üzenetküldési entitások készletének tárolója. Egy új eseményközpont létrehozásakor is létrehozhat a névteret. |
| **Eseményközpont neve** | Az event hubs bemeneti adatokként neve. |
| **Eseményközpont szabályzatának neve** | A megosztott elérési házirendet, amely hozzáférést biztosít az eseményközpontba. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. Ezzel a beállítással a rendszer automatikusan kitölti, kivéve, ha azt választja, adja meg az Eseményközpont-beállítások manuális.|
| **Az Eseményközpont fogyasztói csoportjának** (ajánlott) | Erősen ajánlott minden Stream Analytics-feladat egy eltérő fogyasztói csoportot használnak. Ez a karakterlánc a fogyasztói csoportot kiolvasni az adatokat az event hubs segítségével azonosítja. Ha nem fogyasztói határoz meg csoportot, a Stream Analytics-feladat használ a $Default fogyasztói csoportot.  |
| **Eseményszerializációs formátum** | A szerializálási formátum (JSON, CSV- vagy Avro) a bejövő adatfolyam.  Győződjön meg arról, a JSON-formátumban a Specification igazítja, és nem tartalmazza a tizedes vezető 0. |
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
> Ha az Event Hub használata az IoT Hub útvonalakhoz végpontként, érheti el az IoT Hub medadata használatával a [GetMetadataPropertyValue függvény](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Stream-adatokat az IoT hubról
Az Azure Iot Hub egy nagymértékben méretezhető közzétételi és előfizetési Eseménygyűjtő szolgáltatás IoT-forgatókönyveket optimalizálva.

A Stream Analytics az IoT hubról érkező események alapértelmezett időbélyegzője az IoT hubban, amely érkező az esemény időbélyegzője `EventEnqueuedUtcTime`. Az adatok feldolgozása adatfolyamként időbélyeg használatával, abban az esetben, ha adattartalom kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszót.

### <a name="consumer-groups"></a>Felhasználói csoportok
Be kell állítania minden Stream Analytics az IoT Hub bemeneti szeretné, hogy a saját fogyasztói csoportot. Ha egy feladat a önillesztést tartalmaz, vagy ha több bemenet rendelkezik, bemeneti adatokat olvashatják előfordulhat, hogy egynél több olvasó aktiválásához. Ebben a helyzetben befolyásolja az olvasók egyetlen felhasználói csoportban. Az Azure IoT Hub túllépő / partíciónként fogyasztói csoportot öt olvasók elkerüléséhez célszerű minden Stream Analytics-feladat egy fogyasztói csoportot kijelölje.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Állítsa be az IoT Hub egy bemeneti adatfolyam
A következő táblázat ismerteti az egyes tulajdonságait a **új bemenet** oldal az Azure Portalon az IoT Hub bemeneti adatfolyamként való konfigurálásakor.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti áljel** | Egy rövid nevet, amely hivatkozik a bemeneti használatával a feladat lekérdezésben.|
| **Előfizetés** | Válassza ki az előfizetést, amelyben az IoT Hub erőforrás található. | 
| **IoT Hub** | Az IoT Hub bemeneti adatokként neve. |
| **Végpont** | Az IoT hub végpont.|
| **Megosztott elérési házirend neve** | A megosztott elérési házirendet, amely hozzáférést biztosít az IoT hubnak. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. |
| **Megosztott elérési házirend kulcsa** | A közös hozzáférési kulcs az IoT Hub elérésének engedélyezése.  Ez a beállítás automatikusan kitölti, kivéve, ha akkor választja, adja meg az Iot Hub beállításainak manuális. |
| **Fogyasztói csoport** | Azt javasoljuk, hogy minden Stream Analytics-feladat egy másik fogyasztói csoportot használjon. A fogyasztói csoportot kiolvasni az adatokat az IoT Hub segítségével. Stream Analytics a $Default fogyasztói csoportot használ, hacsak nem ad meg.  |
| **Eseményszerializációs formátum** | A szerializálási formátum (JSON, CSV- vagy Avro) a bejövő adatfolyam.  Győződjön meg arról, a JSON-formátumban a Specification igazítja, és nem tartalmazza a tizedes vezető 0. |
| **Kódolás** | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Esemény tömörítési típusa** | A tömörítési típus, például az egyik sem (alapértelmezett), vagy a GZip, Deflate a beérkező adatfolyam olvasására használják. |


Adatok streamelése az IoT Hub használata esetén elérhető lesz a következő metaadatokat tartalmazó mezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** | A dátum és idő, hogy az esemény feldolgozása. |
| **EventEnqueuedUtcTime** | A dátum és idő, az IoT Hub által fogadott esemény. |
| **PartitionId** | A bemeneti adapter nulla alapú Partícióazonosító. |
| **IoTHub.MessageId** | Vesse össze az IoT Hub kétirányú kommunikáció használt azonosító. |
| **IoTHub.CorrelationId** | Visszajelzés és az IoT Hub visszajelzés használt azonosító. |
| **IoTHub.ConnectionDeviceId** | Ez az üzenet elküldéséhez használt hitelesítési azonosítója. Ez az érték az IoT Hub által servicebound üzenetek van megjelölve. |
| **IoTHub.ConnectionDeviceGenerationId** | Ez az üzenet el lett küldve a hitelesített eszköz létrehozás-azonosítója. Ez az érték az IoT Hub által servicebound üzenetek van megjelölve. |
| **IoTHub.EnqueuedTime** | Az idő, amikor az az üzenetet az IoT Hub által fogadott volt. |
| **IoTHub.StreamId** | A küldő eszköz által hozzáadott egyéni esemény tulajdonság. |


## <a name="stream-data-from-blob-storage"></a>Stream data Blob storage-ból
Az Azure Blob storage költséghatékony és méretezhető megoldást kínál a nagy mennyiségű strukturálatlan adatmennyiséget tárolni a felhőben tárolni forgatókönyvek esetén. A Blob storage szolgáltatásban tárolt adatok általában számít inaktív; adatok azonban Blobadatok adatok adatfolyamként által feldolgozható Stream Analytics. 

Naplófájl feldolgozása a Blob storage bemenetei között a Stream Analytics egy gyakran használt forgatókönyv. Ebben a forgatókönyvben a telemetriai adatok fájlok rendszerből rögzített, és elemzése és feldolgozása történhet az jelentéssel bíró adatokat nyerhet ki kell.

A Blob storage-események a Stream Analytics alapértelmezett időbélyegzője, hogy a blob utolsó módosításának, az időbélyeget, amely `BlobLastModifiedUtcTime`. Az adatok feldolgozása adatfolyamként időbélyeg használatával, abban az esetben, ha adattartalom kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszót. Stream Analytics-feladat kér le adatokat az Azure Blob storage bemeneti másodpercenként blob fájl esetén érhető el. A blob-fájl nem érhető el, ha van egy exponenciális visszatartással 90 másodperc késéssel maximális ideje.

CSV-formátumú bemeneti *megkövetelése* egy fejléc sorra meghatározni az adatkészlet mezőket, és az összes sor fejlécmezők egyedinek kell lennie.

Stream Analytics jelenleg nem támogatja Event Hubs capture vagy az IoT Hub az Azure Storage-tároló egyéni végpont által generált deszerializálása AVRO-üzeneteket.

> [!NOTE]
> Stream Analytics nem támogatja a meglévő blob fájl hozzáadása a tartalom. Stream Analytics tekinthetik meg a fájlok csak egyszer, és minden elvégzett módosításokat a fájlban után a feladat rendelkezik-e olvasási az adatok feldolgozása nem. Ajánlott eljárás, hogy egy blob fájlra vonatkozó összes adatot feltölteni, és ezután további újabb események felvétele egy másik, az új blob fájlt.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>A bemeneti stream konfigurálása a Blob storage 

A következő táblázat ismerteti az egyes tulajdonságait a **új bemenet** oldal az Azure Portalon a Blob storage bemeneti adatfolyamként való konfigurálásakor.

| Tulajdonság | Leírás |
| --- | --- |
| **Bemeneti áljel** | Egy rövid nevet, amely hivatkozik a bemeneti használatával a feladat lekérdezésben. |
| **Előfizetés** | Válassza ki az előfizetést, amelyben az IoT Hub erőforrás található. | 
| **Storage-fiók** | Hol találhatók a blob fájlokat a storage-fiók neve. |
| **Tárfiók kulcsa** | A storage-fiókhoz társított titkos kulcs. Ez a beállítás automatikusan kitölti, kivéve, ha azt választja, adja meg a Blob storage beállításainak manuális. |
| **Tároló** | A tárolót a bemeneti BLOB. Tárolók biztosítják a Microsoft Azure Blob service-ben tárolt blobok logikai jellegű csoportosítását. Az Azure Blob storage szolgáltatáshoz feltölt egy blobot, ha meg kell adnia egy adott blob-tárolót. Választhat **meglévő használata** tároló vagy **új létrehozása** szeretné, hogy létrehozott egy új tárolót.|
| **Elérésiút-minta** (nem kötelező) | A fájl elérési útja, az a megadott tárolóban található blobok helyének azonosításához használt. Az elérési útban a következő három változó egy vagy több példányát adhatja: `{date}`, `{time}`, vagy `{partition}`<br/><br/>1. példa: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>2. példa: `cluster1/logs/{date}`<br/><br/>A `*` karakter nem megengedett érték az az elérési út előtagja. Csak érvényes <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">az Azure blob-karakterek</a> engedélyezettek. Nem tartalmazza a tároló nevének vagy fájlneveket. |
| **Dátumformátum** (nem kötelező) | Ha a dátum változó az útvonalban, a dátumformátum, amelyben a fájlok vannak rendszerezve. Például: `YYYY/MM/DD` |
| **Időformátum** (nem kötelező) |  Ha a idő változó az elérési út, az időformátum, amelyben a fájlok vannak rendszerezve. Jelenleg az egyetlen támogatott érték `HH` órán keresztül. |
| **Eseményszerializációs formátum** | A szerializálási formátum (JSON, CSV- vagy Avro) a bejövő adatfolyam.  Győződjön meg arról, a JSON-formátumban a Specification igazítja, és nem tartalmazza a tizedes vezető 0. |
| **Kódolás** | A fürt megosztott kötetei szolgáltatás és a JSON az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **A tömörítés** | A tömörítési típus, például az egyik sem (alapértelmezett), vagy a GZip, Deflate a beérkező adatfolyam olvasására használják. |

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

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Gyors útmutató: Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
---
title: Az adatfolyam adatok Azure Stream Analytics bevitt
description: További tudnivalók az Azure Stream Analytics adatkapcsolat beállítása. Bemenetek adatok adatfolyamot az események, és is adatokra hivatkoztak.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 2b2ef68622f96d87a25d203d3d67aa0877397072
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>A Stream Analytics bevitt adatfolyam adatok

A Stream Analytics három típusú erőforrások bemeneteként Azure adatfolyamokat első osztályú integrálva van:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

A bemeneti erőforrást is megmarad a azonos Azure-előfizetés a Stream Analytics-feladat, vagy egy másik előfizetésből.

### <a name="compression"></a>Tömörítés
A Stream Analytics minden adatok adatfolyam bemeneti forrás támogatja a tömörítést. Jelenleg támogatott referencia típusok a következők: None, GZip, és a Deflate tömörítést. Tömörítés támogatása nem érhető el a referenciaadatoknál. Az Avro-adatok tömörített megadott bemeneti formátum esetén transzparens módon azt kell kezelni. Adja meg az Avro szerializálási tömörítési típus nem kell. 

## <a name="create-or-edit-inputs"></a>Hozzon létre vagy bemenet szerkesztése
Hozzon létre új bemenetek, és a listában, vagy a folyamatos átviteli feladat meglévő bemenet szerkesztése, használhatja az Azure-portálon:
1. Nyissa meg a [Azure-portálon](https://portal.azure.com) meg és jelölje ki a Stream Analytics-feladat.
2. Válassza ki a **bemenetek** lehetőség szerint a **beállítások** fejléc. 
4. A **bemenetek** laplistákhoz bármely létező bemeneti adatok. 
5. Az a **bemenetek** lapon jelölje be **adatfolyam-bemenet hozzáadása** vagy **bemeneti hivatkozás hozzáadása** részletek lapjának megnyitásához.
6. Válassza ki a részletes adatainak szerkesztéséhez, és válassza ki meglévő bemenete **mentése** egy meglévő bemenet szerkesztése.
7. Válassza ki **teszt** bemeneti részleteit megjelenítő oldalon kell ellenőriznie, hogy a kapcsolati beállítások érvényes, és üzemel. 
8. Kattintson a jobb gombbal a meglévő bemeneti nevét, és válassza ki **az adatokat a bemeneti** további vizsgálat szükséges.


## <a name="stream-data-from-event-hubs"></a>Az Event Hubs adatfolyam adatok

Az Azure Event Hubs biztosít magas szinten méretezhető esemény ingestors közzétételi-feliratkozási. Az eseményközpontok több millió esemény / másodperc, képes összegyűjteni, így egyszerűen feldolgozhatja és elemezheti a nagy mennyiségű adatot a csatlakoztatott eszközök és alkalmazások által létrehozott. Az Event Hubs és a Stream Analytics együtt biztosít egy végpont megoldás a valós idejű elemzési. Az Event Hubs lehetővé teszik, hogy az adatcsatorna-eseményeket az Azure valós időben, és a Stream Analytics-feladatok képes a valós idejű ezeket az eseményeket. Például lehet küldeni webes kattint, érzékelő szivattyútelepek érzékelőinek adatai vagy online alkalmazásnapló-események az Event Hubs. Az Event Hubs használandó a bemeneti adatok adatfolyamok valós idejű szűrés, összesítése és megfelelési Stream Analytics-feladatok is létrehozhat.

Az Event Hubs a Stream Analytics érkező események alapértelmezett időbélyegzője az esemény érkező a központ, amely a timestamp `EventEnqueuedUtcTime`. Az adatok feldolgozására adatfolyamként időbélyeg használatával, abban az esetben hasznos kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszó.

### <a name="consumer-groups"></a>Fogyasztói csoportok
Minden Stream Analytics eseményközpont, adjon meg a saját fogyasztói csoportot rendelkeznie kell beállítani. Ha egy feladat tartalmaz önillesztés, vagy ha több adatbevitele, néhány bemeneti után a több olvasót olvashatják. Ez a helyzet hatással van az olvasók egyetlen felhasználói csoportokban számát. Az Event Hubs túllépő fogyasztói csoportot partíciónként / öt olvasók elkerüléséhez nem ajánlott eljárás a fogyasztói csoportot minden egyes Stream Analytics-feladat megadását. Maximális száma az eseményközpont / 20 felhasználói csoportot is van. További információkért lásd: [hibaelhárítása Azure Stream Analytics Event Hubs érzékelőinek](stream-analytics-event-hub-consumer-groups.md).

### <a name="stream-data-from-event-hubs"></a>Az Event Hubs adatfolyam adatok
Az alábbi táblázat ismerteti az egyes tulajdonságai a **új bemeneti** oldal az Eseményközpontban lévő bemeneti adatfolyam adatokhoz az Azure portálon:

| Tulajdonság | Leírás |
| --- | --- |
| **A bemeneti alias** |Egy rövid nevet, amely hivatkozik a segítségével a feladat lekérdezésben. |
| **Előfizetés** | Válassza ki az előfizetést, amelyben az Event hub erőforrás található. | 
| **Event Hub névtér** | Az Event Hubs-névtér az üzenetküldési entitások készletének tárolója. Egy új eseményközpont létrehozásakor is létrehozhat a névteret. |
| **Eseményközpont neve** | Az event hubs bemenetként használandó neve. |
| **Eseményközpont házirend neve** | A megosztott elérési házirendet, amely hozzáférést biztosít az eseményközpontba. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. Ha nem az Eseményközpont beállításai is megadhat a rendszer automatikusan kitölti ezt a beállítást a manuálisan.|
| **Event Hub fogyasztói csoportot** (ajánlott) | Ajánlott egy különálló fogyasztói csoportot használni minden egyes Stream Analytics-feladathoz. Ez a karakterlánc az event hubs származó adatok használandó fogyasztói csoportot határozza meg. Ha nincs felhasználói csoportban van megadva, a Stream Analytics-feladat használ a $Default fogyasztói csoportot.  |
| **Esemény szerializálási formátum** | A szerializálási formátum (JSON, CSV vagy Avro) a bejövő adatfolyam.  Győződjön meg arról, a JSON formátum igazodik a megadását, és nem tartalmazza a decimális számok kezdő 0. |
| **Kódolás** | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Esemény tömörítési típus** | A használni kívánt olvassa el a bejövő adatfolyam, például a None (alapértelmezett), vagy a GZip, Deflate tömörítést típusát. |

Ha az adatok az Eseményközpont adatfolyam-bemenet, elérhető lesz a következő metaadatmezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** |A dátum és idő, amely az esemény feldolgozása Stream Analytics. |
| **EventEnqueuedUtcTime** |A dátum és idő, az esemény az Event Hubs által fogadott. |
| **PartitionId** |A bemeneti adapter nulla alapú Partícióazonosító. |

Például használja ezeket a mezőket, írhat egy lekérdezést az alábbi példához hasonló:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Használatakor az Event Hubs végpontjaként IoT Hub útvonalak tudja elérni az IoT-központ medadata használatával a [GetMetadataPropertyValue függvény](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Az IoT-központ adatfolyam adatait
Az Azure Iot Hub egy kiválóan méretezhető közzététele előfizetés IoT-forgatókönyvek esetén az optimalizált eseménybetöltőnek.

Az IoT-központ a Stream Analytics érkező események alapértelmezett időbélyegzője a timestamp, az esemény az IoT Hub, amely a érkező `EventEnqueuedUtcTime`. Az adatok feldolgozására adatfolyamként időbélyeg használatával, abban az esetben hasznos kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszó.

> [!NOTE]
> Csak a küldött üzenetek egy `DeviceClient` tulajdonság dolgozhatók fel.
> 

### <a name="consumer-groups"></a>Fogyasztói csoportok
Úgy kell konfigurálni minden Stream Analytics IoT Hub bemeneti kell rendelkeznie a saját felhasználói csoportban. Ha egy feladat tartalmaz önillesztés, vagy ha több adatbevitele, néhány bemeneti után a több olvasót olvashatják. Ez a helyzet hatással van az olvasók egyetlen felhasználói csoportokban számát. Az Azure IoT Hub túllépő fogyasztói csoportot partíciónként / öt olvasók elkerüléséhez nem ajánlott eljárás a fogyasztói csoportot minden egyes Stream Analytics-feladat megadását.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>A bemeneti adatfolyamban az IoT-központ konfigurálása
Az alábbi táblázat ismerteti az egyes tulajdonságai a **új bemeneti** lap az Azure-portálon az IoT-központ bemeneti adatfolyamként való konfigurálásakor.

| Tulajdonság | Leírás |
| --- | --- |
| **A bemeneti alias** | Egy rövid nevet, amely hivatkozik a segítségével a feladat lekérdezésben.|
| **Előfizetés** | Válassza ki az előfizetést, amelyben az IoT-központ erőforrás található. | 
| **IoT Hub** | Az IoT Hub bemeneti használandó neve. |
| **végpont** | Az IoT Hub végpontja.|
| **Megosztott elérési házirend neve** | A megosztott elérési házirendet, amely hozzáférést biztosít az IoT-központot. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. |
| **Megosztott elérési házirend kulcs** | A közös hozzáférési kulcs segítségével engedélyezi a hozzáférést az IoT-központ részére.  Ez a beállítás automatikusan kitölti, kivéve ha a lehetőséget, ha az Iot Hub beállításainak manuális. |
| **Felhasználói csoport** | Erősen ajánlott használni, amikor egy másik felhasználói csoport minden egyes Stream Analytics-feladat. A fogyasztói csoportot az IoT Hub származó adatok szolgál. A Stream Analytics a $Default fogyasztói csoportot használ, hacsak nem ad meg.  |
| **Esemény szerializálási formátum** | A szerializálási formátum (JSON, CSV vagy Avro) a bejövő adatfolyam.  Győződjön meg arról, a JSON formátum igazodik a megadását, és nem tartalmazza a decimális számok kezdő 0. |
| **Kódolás** | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Esemény tömörítési típus** | A használni kívánt olvassa el a bejövő adatfolyam, például a None (alapértelmezett), vagy a GZip, Deflate tömörítést típusát. |


Az IoT-központ adatfolyam adatok használata esetén elérhető lesz a következő metaadatmezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** | A dátum és idő, amely az esemény feldolgozása. |
| **EventEnqueuedUtcTime** | A dátum és idő, az esemény az IoT-központ által fogadott. |
| **PartitionId** | A bemeneti adapter nulla alapú Partícióazonosító. |
| **IoTHub.MessageId** | Összefüggéseket kétirányú kommunikációt az IoT hubon használt azonosító. |
| **IoTHub.CorrelationId** | A üzenet válaszok és az IoT hubon visszajelzés használt azonosító. |
| **IoTHub.ConnectionDeviceId** | Ez az üzenet küldhető a hitelesítési azonosítója. Ez az érték az az IoT Hub servicebound üzenetek van megjelölve. |
| **IoTHub.ConnectionDeviceGenerationId** | A Létrehozás-azonosítója a hitelesített eszköz, amelyet ez az üzenet küldése használtak. Ez az érték az az IoT Hub servicebound üzenetek van megjelölve. |
| **IoTHub.EnqueuedTime** | Az az idő, amikor az üzenetet az IoT Hub kapott. |
| **IoTHub.StreamId** | A küldő eszköz által hozzáadott egyéni esemény tulajdonság. |


## <a name="stream-data-from-blob-storage"></a>Adatfolyam-adatokat a Blob storage
Nagy mennyiségű strukturálatlan adatok a felhőben tárolt forgatókönyvek esetén az Azure Blob storage egy költséghatékony, méretezhető megoldást kínál. Blob Storage tárolóban lévő adatok általában számít inaktív adatok. Azonban Blobadatok feldolgozási adatok adatfolyamként Stream Analytics. 

Napló feldolgozása a Blob storage bemeneti adatok használata a Stream Analytics leggyakrabban használt forgatókönyv. Ebben a forgatókönyvben a telemetriai adatok fájlok rendszerből rögzített, és kell elemezni és feldolgozott lekérdezhetik a fontos adatokat nyerhet ki.

A Blob storage-események a Stream Analytics alapértelmezett időbélyegzője, hogy a blob utolsó módosításának, timestamp, amely `BlobLastModifiedUtcTime`. Az adatok feldolgozására adatfolyamként időbélyeg használatával, abban az esetben hasznos kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszó.

CSV-formátumú bemeneti *szükséges* adható meg a következő adatkészletnél mezők és minden sor fejlécmezők fejlécsor egyedinek kell lennie.

A Stream Analytics jelenleg nem támogatja az Event Hubs rögzítési vagy IoT Hub Azure-tárolót egyéni végpont által előállított deszerializálási AVRO üzeneteket.

> [!NOTE]
> A Stream Analytics nem támogatja a blob meglévő fájl hozzáadása tartalmat. A Stream Analytics csak egyszer minden egyes fájl tekinthetik, és a módosítások után a feladat számára az adatok olvasása a fájlban előforduló nincsenek feldolgozva. Ajánlott egy blob-fájlra vonatkozó összes adatot feltölteni, majd adja hozzá az újabb a további eseményeket egy másik, új blob fájlba.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>A bemeneti adatfolyam Blob-tároló konfigurálása 

Az alábbi táblázat ismerteti az egyes tulajdonságai a **új bemeneti** oldal az Azure portálon Blob-tároló bemeneti adatfolyamként való konfigurálásakor.

| Tulajdonság | Leírás |
| --- | --- |
| **A bemeneti alias** | Egy rövid nevet, amely hivatkozik a segítségével a feladat lekérdezésben. |
| **Előfizetés** | Válassza ki az előfizetést, amelyben az IoT-központ erőforrás található. | 
| **Storage-fiók** | A tárfiók, ahol a blob-fájlok találhatók neve. |
| **Tárfiók kulcsa** | A storage-fiókjához tartozó titkos kulcsot. Ez a beállítás automatikusan kitölti, kivéve ha a lehetőséget, ha a Blob storage beállításainak manuális. |
| **Tároló** | A bemeneti BLOB a tárolóban. Tárolók adja meg a Microsoft Azure Blob szolgáltatásban tárolt blobok logikai csoportosítását. Egy blob az Azure Blob storage szolgáltatásban való feltöltésekor meg kell adnia, hogy a blob tárolója. Ön választhatja **használata meglévő** tároló vagy **hozzon létre új** szeretné, hogy létrehozott egy új tároló.|
| **Elérési út mintája** (nem kötelező) | A fájl elérési útja, a megadott tárolóban található blobok helyének azonosításához használt. Az elérési útban, adja meg a következő három változó egy vagy több példányát: `{date}`, `{time}`, vagy `{partition}`<br/><br/>1. példa: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>2. példa: `cluster1/logs/{date}`<br/><br/>A `*` karakter értéke nem engedélyezett az elérési út előtag. Csak érvényes <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob karakterek</a> engedélyezettek. |
| **Dátumformátum** (nem kötelező) | Ha a dátum változó az elérési út, a dátumformátum, amelyben a fájlok vannak rendezve. Példa: `YYYY/MM/DD` |
| **Időformátum** (nem kötelező) |  Ha a idő változó az elérési út, az időformátum, amelyben a fájlok vannak rendezve. Jelenleg az egyetlen támogatott érték `HH` órán keresztül. |
| **Esemény szerializálási formátum** | A szerializálási formátum (JSON, CSV vagy Avro) a bejövő adatfolyam.  Győződjön meg arról, a JSON formátum igazodik a megadását, és nem tartalmazza a decimális számok kezdő 0. |
| **Kódolás** | A fürt megosztott kötetei szolgáltatás és a JSON UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Tömörítés** | A használni kívánt olvassa el a bejövő adatfolyam, például a None (alapértelmezett), vagy a GZip, Deflate tömörítést típusát. |

Ha az adatok egy Blob storage forrásból, akkor is hozzáférhet a következő metaadat-mezők a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **BlobName** |A bemeneti blob, amely az esemény származik neve. |
| **EventProcessedUtcTime** |A dátum és idő, amely az esemény feldolgozása Stream Analytics. |
| **BlobLastModifiedUtcTime** |A dátum és idő, a blob utolsó módosításának. |
| **PartitionId** |A bemeneti adapter nulla alapú Partícióazonosító. |

Például használja ezeket a mezőket, írhat egy lekérdezést az alábbi példához hasonló:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Gyors üzembe helyezés: A Stream Analytics-feladat létrehozása az Azure portál használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

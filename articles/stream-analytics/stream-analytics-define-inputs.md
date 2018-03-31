---
title: 'Adatkapcsolat: adatfolyam-bemenet egy esemény adatfolyamból |} Microsoft Docs'
description: Ismerje meg a Stream Analytics "bemenetek" nevű adatkapcsolatot beállítása. Bemenetek adatok adatfolyamot az események, és is adatokra hivatkoztak.
keywords: az adatfolyam, adatkapcsolat, eseményfelhasználó
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/11/2017
ms.author: sngun
ms.openlocfilehash: 0ddc3187e9fc0664838dd07f781f4d7e2e4a7fe0
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Adatkapcsolat: data Stream Analytics az események adatfolyam-bemenet megismerése
A Stream Analytics-feladat adatok kapcsolat nem olyan adatfolyamot kell megadni az adatforrás, amelynek a neve a feladat események *bemeneti*. A Stream Analytics rendelkezik első osztályú integráció az Azure data stream móddal, többek között a [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/), és [Azure Blob Storage tárolóban](https://azure.microsoft.com/services/storage/blobs/). A bemeneti források lehet az analytics-feladat az azonos Azure-előfizetés, vagy egy másik előfizetést.

## <a name="data-input-types-data-stream-and-reference-data"></a>Adatok bemeneti típussal: Data stream és hivatkozási adatok
Mivel az adatokat a rendszer előkészítésre továbbít egy adatforrást, a Stream Analytics-feladat által használt, és valós időben feldolgozott. Bemeneti adatok meg vannak osztva kétféle: adatok adatfolyam-bemenet, és az adatok bemenetek hivatkozik.

### <a name="data-stream-inputs"></a>Adatfolyam-bemenet
Adatfolyam események unbounded sorozatát adott idő alatt. Stream Analytics-feladatok tartalmaznia kell legalább egy adatfolyam-bemenetre. Az Event Hubs, IoT Hub és a Blob storage adatforrások adatfolyam bemeneti is támogatottak. Az Event hubs segítségével több eszközökön és szolgáltatásokon szolgáltatásból összegyűjtsék az eseményfolyamokat. Ezekbe az adatfolyamokba közé tartozik a közösségi média tevékenység hírcsatornák, a készlet kereskedelmi információ vagy az érzékelők. IoT-központok adatokat gyűjteni az eszközök internetes hálózatát (IoT) forgatókönyvekben csatlakoztatott eszközök vannak optimalizálva.  A BLOB storage tömeges eseményközpontokból adatfolyamként, például a naplófájlok bemeneti forrása is használható.  

### <a name="reference-data"></a>Referenciaadat
A Stream Analytics is támogatja a bemeneti néven *referenciaadatok*. Ez az kiegészítő adatokat, amelyek statikus vagy lassan módosítja. A korrelációs és keresések végrehajtásához általában szolgál. Például előfordulhat, hogy csatlakozik a adatok az adatok a referenciaadatok az adatfolyam-bemenetre mint statikus értékek kereshető SQL illesztés kell elvégeznie. Az Azure Blob storage jelenleg az egyetlen támogatott bemeneti forrás a referenciaadatoknál. Hivatkozási adatforrás a BLOB-adatobjektumok korlátozódnak 100 MB-nál.

Adatok bemeneti hivatkozást létrehozni, lásd: [használata referenciaadatok](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Tömörítés

Az Azure Stream Analytics összes adatfolyam bemeneti adatforrások (Event Hubs, az IoT Hub és a Blob storage) keresztül támogatja a tömörítést. Ez a szolgáltatás ad hozzá egy új legördülő lehetőség a **új bemeneti** panel az Azure portálon, így kérheti az adatfolyamokat tömörítése. Jelenleg támogatott referencia típusok van –, hogy nincs GZip, és a Deflate tömörítést. Tömörítés támogatása nem érhető el a referenciaadatoknál.

Adja meg az Avro szerializálási tömörítési típus nem kell. Ha a bemeneti az Avro-adatok tömörített, kezelt transzparens módon. 

## <a name="create-data-stream-input-from-event-hubs"></a>Az Event Hubs adatfolyam-bemenetre létrehozása

Az Azure Event Hubs biztosít magas szinten méretezhető esemény ingestors közzétételi-feliratkozási. Az eseményközpontok több millió esemény / másodperc, képes összegyűjteni, így egyszerűen feldolgozhatja és elemezheti a nagy mennyiségű adatot a csatlakoztatott eszközök és alkalmazások által létrehozott. Az Event Hubs és a Stream Analytics együttesen biztosítja egy végpont megoldás az valós idejű elemzési – az Event Hubs lehetővé teszik, hogy az adatcsatorna-eseményeket az Azure valós időben, és a Stream Analytics-feladatok képes a valós idejű ezeket az eseményeket. Például lehet küldeni webes kattint, érzékelő szivattyútelepek érzékelőinek adatai vagy online alkalmazásnapló-események az Event Hubs. Az Event Hubs használandó a bemeneti adatok adatfolyamok valós idejű szűrés, összesítése és megfelelési Stream Analytics-feladatok is létrehozhat.

Az Event Hubs a Stream Analytics érkező események alapértelmezett időbélyegzője az esemény érkező a központ, amely a timestamp `EventEnqueuedUtcTime`. Az adatok feldolgozására adatfolyamként időbélyeg használatával, abban az esetben hasznos kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszó.

### <a name="consumer-groups"></a>Fogyasztói csoportok
Minden Stream Analytics eseményközpont, adjon meg a saját fogyasztói csoportot rendelkeznie kell beállítani. Ha egy feladat tartalmaz önillesztés, vagy ha több adatbevitele, néhány bemeneti után a több olvasót olvashatják. Ez a helyzet hatással van az olvasók egyetlen felhasználói csoportokban számát. Az Event Hubs túllépő fogyasztói csoportot partíciónként / öt olvasók elkerüléséhez nem ajánlott eljárás a fogyasztói csoportot minden egyes Stream Analytics-feladat megadását. Maximális száma az eseményközpont / 20 felhasználói csoportot is van. További információkért lásd: [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Az eseményközpontok beállítása a bemeneti adatfolyam
Az alábbi táblázat ismerteti az egyes tulajdonságai a **új bemeneti** panel az Azure-portálon az eseményközpontok bemenetként konfigurálásakor.

| Tulajdonság | Leírás |
| --- | --- |
| **A bemeneti alias** |Egy rövid nevet, amely hivatkozik a segítségével a feladat lekérdezésben. |
| **Service bus-névtér** |Az Azure Service Bus-névtér, amely az üzenetküldési entitások készletének tárolója. Egy új eseményközpont létrehozásakor egy Szolgáltatásbusz-névtér is létrehozhat. |
| **Eseményközpont neve** |Az event hubs bemenetként használandó neve. |
| **Eseményközpont házirend neve** |A megosztott elérési házirendet, amely elérhető az eseményközpontba. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. |
| **Event hub fogyasztói csoportot** (nem kötelező) |A használatára az event hubs származó adatok fogyasztói csoportot. Ha nincs felhasználói csoportban van megadva, a Stream Analytics-feladat használ az alapértelmezett felhasználói csoport. Azt javasoljuk, hogy használja-e egy külön felhasználói csoport minden egyes Stream Analytics-feladathoz. |
| **Esemény szerializálási formátum** |A szerializálási formátum (JSON, CSV vagy Avro) a bejövő adatfolyam. |
| **Kódolás** | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Tömörítés** (nem kötelező) | A tömörítési típusának (nincs, vagy a GZip, Deflate) a bejövő adatfolyam. |

Ha az adatok az eseményközpontban lévő, elérhető lesz a következő metaadatmezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** |A dátum és idő, amely az esemény feldolgozása Stream Analytics. |
| **EventEnqueuedUtcTime** |A dátum és idő, az esemény az Event Hubs által fogadott. |
| **PartitionId** |A bemeneti adapter nulla alapú Partícióazonosító. |

Például használja ezeket a mezőket, írhat egy lekérdezést az alábbi példához hasonló:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Használatakor az Event Hubs végpontjaként IoT Hub útvonalak tudja elérni az IoT-központ medadata használatával a [GetMetadataPropertyValue függvény](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Adatfolyam-bemenetre IoT Hub létrehozása
Az Azure Iot Hub egy kiválóan méretezhető közzététele előfizetés IoT-forgatókönyvek esetén az optimalizált eseménybetöltőnek.

Az IoT-központ a Stream Analytics érkező események alapértelmezett időbélyegzője a timestamp, az esemény az IoT hub, amely a érkező `EventEnqueuedUtcTime`. Az adatok feldolgozására adatfolyamként időbélyeg használatával, abban az esetben hasznos kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszó.

> [!NOTE]
> Csak a küldött üzenetek egy `DeviceClient` tulajdonság dolgozhatók fel.
> 
> 

### <a name="consumer-groups"></a>Fogyasztói csoportok
Minden Stream Analytics IoT-központ rendelkezik saját felhasználói csoportban megadott úgy kell konfigurálni. Ha egy feladat tartalmaz önillesztés, vagy ha több adatbevitele, néhány bemeneti után a több olvasót olvashatják. Ez a helyzet hatással van az olvasók egyetlen felhasználói csoportokban számát. Az Azure IoT Hub túllépő fogyasztói csoportot partíciónként / öt olvasók elkerüléséhez nem ajánlott eljárás a fogyasztói csoportot minden egyes Stream Analytics-feladat megadását.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>A bemeneti adatfolyamban az IoT-központ konfigurálása
Az alábbi táblázat ismerteti az egyes tulajdonságai a **új bemeneti** panel az Azure-portálon az IoT-központ bemenetként konfigurálásakor.

| Tulajdonság | Leírás |
| --- | --- |
| **A bemeneti alias** |Egy rövid nevet, amely hivatkozik a segítségével a feladat lekérdezésben.|
| **Az IoT-központ** |Az IoT hub bemeneti használandó neve. |
| **végpont** |Az IoT hub végpontja.|
| **Megosztott elérési házirend neve** |A megosztott elérési házirendet, amely az IoT hub hozzáférést biztosít. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. |
| **Megosztott elérési házirend kulcs** |A közös hozzáférési kulcs segítségével engedélyezi a hozzáférést az IoT-központ részére. |
| **Felhasználói csoport** (nem kötelező) |A használatára az IoT hub származó adatok fogyasztói csoportot. Ha nincs felhasználói csoportban van megadva, a Stream Analytics-feladat használja az alapértelmezett felhasználói csoport. Azt javasoljuk, hogy használja-e egy másik felhasználói csoport minden egyes Stream Analytics-feladathoz. |
| **Esemény szerializálási formátum** |A szerializálási formátum (JSON, CSV vagy Avro) a bejövő adatfolyam. |
| **Kódolás** |Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| **Tömörítés** (nem kötelező) | A tömörítési típusának (nincs, vagy a GZip, Deflate) a bejövő adatfolyam. |

Az adatokat az IoT-központ származik, amikor hozzáfér a következő metaadatmezőket a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **EventProcessedUtcTime** |A dátum és idő, amely az esemény feldolgozása. |
| **EventEnqueuedUtcTime** |A dátum és idő, az esemény az IoT-központ által fogadott. |
| **PartitionId** |A bemeneti adapter nulla alapú Partícióazonosító. |
| **IoTHub.MessageId** | Összefüggéseket kétirányú kommunikációt az IoT hubon használt azonosító. |
| **IoTHub.CorrelationId** |A üzenet válaszok és az IoT hubon visszajelzés használt azonosító. |
| **IoTHub.ConnectionDeviceId** |Ez az üzenet küldhető a hitelesítési azonosítója. Ez az érték az az IoT hub servicebound üzenetek van megjelölve. |
| **IoTHub.ConnectionDeviceGenerationId** |A Létrehozás-azonosítója a hitelesített eszköz, amelyet ez az üzenet küldése használtak. Ez az érték az az IoT hub servicebound üzenetek van megjelölve. |
| **IoTHub.EnqueuedTime** |Az az idő, amikor az üzenetet az IoT hub kapott. |
| **IoTHub.StreamId** |A küldő eszköz által hozzáadott egyéni esemény tulajdonság. |


## <a name="create-data-stream-input-from-blob-storage"></a>Adatfolyam-bemenetre Blob-tároló létrehozása
Nagy mennyiségű strukturálatlan adatok a felhőben tárolt forgatókönyvek esetén az Azure Blob storage egy költséghatékony, méretezhető megoldást kínál. Blob Storage tárolóban lévő adatok általában számít inaktív adatok. Azonban dolgozható adatok adatfolyamként Stream Analytics. A Blob storage bemenetek Stream Analytics a jellemző forgatókönyv napló feldolgozásával. Ebben a forgatókönyvben telemetriai adatokat a rendszerből rögzített, és kell elemezni és feldolgozott lekérdezhetik a fontos adatokat nyerhet ki.

A Blob storage-események a Stream Analytics alapértelmezett időbélyegzője, hogy a blob utolsó módosításának, timestamp, amely `BlobLastModifiedUtcTime`. Az adatok feldolgozására adatfolyamként időbélyeg használatával, abban az esetben hasznos kell használnia a [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) kulcsszó.

CSV-formátumú bemeneti *szükséges* a fejlécsor adatkészlet mezők definiálásához. Minden sor fejlécmezők emellett egyedinek kell lennie.

> [!NOTE]
> A Stream Analytics nem támogatja a blob meglévő fájl hozzáadása tartalmat. A Stream Analytics csak egyszer minden egyes fájl tekinthetik, és a módosítások után a feladat számára az adatok olvasása a fájlban előforduló nincsenek feldolgozva. Ajánlott egy blob-fájlra vonatkozó összes adatot feltölteni, majd adja hozzá az újabb a további eseményeket egy másik, új blob fájlba.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>A bemeneti adatfolyamban Blob-tároló konfigurálása

Az alábbi táblázat ismerteti az egyes tulajdonságai a **új bemeneti** panel az Azure-portálon bemeneti Blob-tároló konfigurálásakor.

| Tulajdonság | Leírás |
| --- | --- |
| **A bemeneti alias** | Egy rövid nevet, amely hivatkozik a segítségével a feladat lekérdezésben. |
| **Storage-fiók** | A tárfiók, ahol a blob-fájlok találhatók neve. |
| **Tárfiók kulcsa** | A storage-fiókjához tartozó titkos kulcsot. |
| **Container** | A bemeneti BLOB a tárolóban. Tárolók adja meg a Microsoft Azure Blob szolgáltatásban tárolt blobok logikai csoportosítását. Egy blob az Azure Blob storage szolgáltatásban való feltöltésekor meg kell adnia, hogy a blob tárolója. |
| **Elérési út mintája** (nem kötelező) | A fájl elérési útja, a megadott tárolóban található blobok helyének azonosításához használt. Az elérési útban, adja meg a következő három változó egy vagy több példányát: `{date}`, `{time}`, vagy `{partition}`<br/><br/>1. példa: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>2. példa: `cluster1/logs/{date}`<br/><br/>A `*` karakter értéke nem engedélyezett az elérési út előtag. Csak érvényes <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob karakterek</a> engedélyezettek. |
| **Dátumformátum** (nem kötelező) | Ha a dátum változó az elérési út, a dátumformátum, amelyben a fájlok vannak rendezve. Példa: `YYYY/MM/DD` |
| **Időformátum** (nem kötelező) |  Ha a idő változó az elérési út, az időformátum, amelyben a fájlok vannak rendezve. Jelenleg az egyetlen támogatott érték `HH`. |
| **Esemény szerializálási formátum** | A szerializálási formátum (JSON, CSV vagy Avro) a bejövő adatfolyamokhoz. |
| **Kódolás** | A fürt megosztott kötetei szolgáltatás és a JSON UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| **Tömörítés** (nem kötelező) | A tömörítési típusának (nincs, vagy a GZip, Deflate) a bejövő adatfolyam. |

Ha az adatok egy Blob storage forrásból, akkor is hozzáférhet a következő metaadat-mezők a Stream Analytics-lekérdezés:

| Tulajdonság | Leírás |
| --- | --- |
| **BlobName** |A bemeneti blob, amely az esemény származik neve. |
| **EventProcessedUtcTime** |A dátum és idő, amely az esemény feldolgozása Stream Analytics. |
| **BlobLastModifiedUtcTime** |A dátum és idő, a blob utolsó módosításának. |
| **PartitionId** |A bemeneti adapter nulla alapú Partícióazonosító. |

Például használja ezeket a mezőket, írhat egy lekérdezést az alábbi példához hasonló:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Segítség kérése
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
Hogy megismerkedett adatok kapcsolat beállításai az Azure-ban a Stream Analytics-feladatok. A Stream Analytics kapcsolatos további információkért lásd:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

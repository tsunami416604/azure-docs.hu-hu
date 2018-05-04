---
title: Rögzítheti az Azure Event Hubs áttekintése |} Microsoft Docs
description: Az Event Hubs rögzítése telemetrikus adatokat rögzítése
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 00eee302cc15d94ec62f5f3332e18ee2df24f5cd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="azure-event-hubs-capture"></a>Az Azure Event Hubs rögzítése

Az Azure Event Hubs rögzítése lehetővé teszi, hogy automatikusan a streamelési adatok az Event Hubs egy [Azure Blob Storage tárolóban](https://azure.microsoft.com/services/storage/blobs/) vagy [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) hozzáadott rugalmasan választhassa a kiválasztott fiók egy alkalommal vagy méret időköz megadása. Rögzítési beállítása gyors, nem futtatható. felügyeleti költségek van, és automatikusan méretezi az Event Hubs [átviteli egységek](event-hubs-features.md#capacity). Event Hubs rögzítése a legegyszerűbb módja a streamelési adatok betöltése az Azure, és lehetővé teszi az adatok feldolgozása és nem a rögzítés összpontosíthat.

Event Hubs rögzítése lehetővé teszi az azonos adatfolyamon valós idejű és kötegelt alapú folyamatok feldolgozni. Ez azt jelenti, hogy idővel igényeknek nő megoldásokat hozhat létre. Batch-alapú rendszerek ma felé jövőbeli valós idejű feldolgozással követheti most felépítése, vagy hozzá szeretne adni egy hatékony cold útvonalat ad egy meglévő valós idejű megoldáshoz, Event Hubs rögzítése lehetővé teszi a könnyebb adatfolyam használata.

## <a name="how-event-hubs-capture-works"></a>Event Hubs rögzítése működése

Az Event Hubs egy idő-megőrzési telemetriai érkező, egy elosztott napló hasonló tartós puffer. A kulcs az Event Hubs skálázás a [particionált felhasználói modell](event-hubs-features.md#partitions). Mindegyik partíció az adatok egy független szegmense, és egymástól függetlenül felhasznált. Idővel ezek az adatok elavulnak, ki, a konfigurálható megőrzési időtartam alapján. Emiatt egy adott eseményközpont soha nem megtelik "túl."

Event Hubs rögzítése lehetővé teszi a saját Azure Blob storage-fiók és a tároló vagy az Azure Data Lake Store-fiókot, a rögzített adatok tárolására használt megadását. Ezek a fiókok lehet az eseményközpont ugyanabban a régióban, vagy egy másik régióban, a rugalmasságot az Event Hubs rögzítése funkció hozzáadása.

A rögzített adatok [Apache Avro] [ Apache Avro] formátum: kompakt, gyors és bináris formátum, amely a beágyazott sémák gazdag adatstruktúrák biztosít. Ezt a formátumot a Hadoop ökoszisztémájának, a Stream Analytics és az Azure Data Factory széles körben használja. Az Avro kezelésével kapcsolatos további információk a cikk későbbi részében érhető el.

### <a name="capture-windowing"></a>Leképezési rögzítése

Event Hubs rögzítése lehetővé teszi, hogy meg kell adnia egy ablak rögzítése szabályozására. Ebben az ablakban a minimális és időpontjának beállítása "első wins házirendnek," ami azt jelenti, hogy az első észlelt az eseményindító a rögzítési művelet. Ha egy 15 perc, 100 MB Rögzítés ablak, és 1 MB protokollüzenetek másodpercenkénti, a méret ablak eseményindítók a időszak előtt. Mindegyik partíció egymástól függetlenül rögzíti, és írási műveletek befejezett blokkblob rögzítési, időpontjában, ahol a rögzítési időköz történt alkalommal nevű. A tároló elnevezési egyezménynek a következőképpen történik:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Vegye figyelembe, hogy a dátumértékek helyeken nullából; egy példa fájlnév lehet:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Átviteli egységek méretezhetők

Event Hubs forgalom által szabályozott [átviteli egységek](event-hubs-features.md#capacity). Egy átviteli egység lehetővé teszi, hogy 1 MB másodpercenkénti bemenő és kimenő forgalom kétszer adott mennyiségű második vagy 1000 esemény. Standard Event Hubs 1 – 20 átviteli egység konfigurálhatók, és vásárolhat további, a kvóta növeléséhez [támogatási kérelem][support request]. Használat meghaladja a megvásárolt átviteli egységek folyamatban van. Event Hubs rögzítése másol adatokat közvetlenül a belső tárolóban az Event Hubs átviteli egység kilépő kvóták kihagyásával, és a kimenő forgalom mentése más feldolgozási olvasók, például a Stream Analytics vagy Spark.

Beállítása után Event Hubs rögzítése automatikusan fut, amikor az első esemény küldi el, és továbbra is futnak. Könnyebben tudnia, hogy működik-e a folyamat az alárendelt feldolgozásra, az Event Hubs üres fájlok ír, amikor nincsenek adatok. Ez a folyamat előre jelezhető ütemben történik és jelző, amely képes a kötegelt processzor biztosít.

## <a name="setting-up-event-hubs-capture"></a>Event Hubs rögzítése beállítása

Rögzítési konfigurálhatja a event hub létrehozási ideje az az [Azure-portálon](https://portal.azure.com), vagy az Azure Resource Manager-sablonok használatával. További információkért tekintse át a következő cikkeket:

- [Engedélyezze az Event Hubs rögzítheti az Azure portál használatával](event-hubs-capture-enable-through-portal.md)
- [Hozzon létre egy Event Hubs névtér eseményközpontban, és engedélyezze a rögzítést az Azure Resource Manager-sablonok](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>A rögzített fájlok fel, és az Avro használata

Az Avro formátum, a megadott időszak megadott fájlok Event Hubs rögzítése hoz létre. Megtekintheti ezeket a fájlokat, mint bármely eszköz [Azure Tártallózó][Azure Storage Explorer]. Letöltheti a fájlokat helyileg őket.

Az Event Hubs rögzítése által előállított fájlokat a következő az Avro-séma rendelkezik:

![][3]

Egy egyszerű módja felfedezése, mely az Avro-fájlok a [Avro eszközök] [ Avro Tools] az Apache jar. A jar a letöltés után megtekintheti a séma egy konkrét az Avro-fájl a következő parancs futtatásával:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Ez a parancs visszaadja

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Az Avro eszközök segítségével a fájl konvertálása JSON formátumú, és végezze el a más feldolgozása.

Speciális feldolgozási műveleteket, töltse le és telepítse a Avro a választott platform számára. A cikk írásának időpontjában érhetők el megvalósítások C, C++, C\#, Java, NodeJS, Perl, PHP, Python vagy Ruby.

Apache Avro rendelkezik teljes bevezetés útmutatói [Java] [ Java] és [Python][Python]. Is a [Bevezetés az Event Hubs rögzítése](event-hubs-capture-python.md) cikk.

## <a name="how-event-hubs-capture-is-charged"></a>Hogyan Event Hubs rögzítése feladata

Event Hubs rögzítése forgalmi díjas hasonlóan az átviteli egységek: mint egy óránkénti kell fizetni. Az elsők között közvetlenül a névtér megvásárolt átviteli egységek számával arányos. Átviteli egységek növelhető és csökkenthető, Event Hubs rögzítése mérőszámok növelheti és csökkentheti a megfelelő teljesítmény elérése érdekében. A mérőszámok párhuzamosan történik. Díjszabása, lásd: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>További lépések

A legegyszerűbben az adatok lekérése az Azure Event Hubs rögzítése. Az Azure Data Lake, az Azure Data Factory és az Azure HDInsight, kötegfeldolgozási hajthat végre, és más jól ismert eszközökkel és a platformok elemzés, bármilyen léptékben van szüksége.

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Küldés és fogadás események első lépései](event-hubs-dotnet-framework-getstarted-send.md)
* [Event Hubs – áttekintés][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md

---
title: Streamelési események – Azure Event Hubs Capture |} A Microsoft Docs
description: Ez a cikk áttekintést, amely lehetővé teszi rögzíthető eseményekkel – keresztül az Azure Event Hubs rögzítési funkciója.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 442ceab68851dc108d327cdf212dcf58d5b31084
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008565"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Az Azure Event Hubs az Azure Blob Storage vagy az Azure Data Lake Storage keresztül események rögzítése
Az Azure Event Hubs lehetővé teszi, hogy automatikusan rögzítheti a streamelt adatokat az Event hubs- [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) vagy [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) hozzáadott rugalmasságával a kiválasztott fiók Adjon meg egy idő- vagy méretbeli intervallumokat. Rögzítés beállítása a gyors, nincsenek a futtatáshoz felügyeleti költségek, és automatikusan átméreteződik az Event Hubs [átviteli egységek](event-hubs-scalability.md#throughput-units). Az Event Hubs Capture legegyszerűbb módja a streamelési adatok betöltése az Azure-ba, és lehetővé teszi, hogy fókusz adatfeldolgozási helyett az adatváltozások rögzítése.

Event Hubs rögzítés funkciója lehetővé teszi ugyanazon streamben-folyamatok valós idejű és kötegelt feldolgozásához. Ez azt jelenti, hogy idővel igényeinek hozhat létre megoldásokat, amelyek növekszik. E batch-alapú rendszereket jelenleg kövesse figyelemmel valós idejű feldolgozás jövőbeli felé fejleszt, vagy egy hatékony ritka elérésű útvonal hozzáadása egy meglévő valós idejű megoldást szeretne, streamelési adatok könnyebb munka az Event Hubs Capture révén.

## <a name="how-event-hubs-capture-works"></a>Az Event Hubs Capture működése

Event Hubs szolgáltatás egy idő-megőrzési puffert a telemetriai adatok bejövő, elosztott a napló a hasonló. Skálázás az Event Hubs kulcsa a [particionált felhasználói modell](event-hubs-scalability.md#partitions). Mindegyik partíció egy független szegmenst az adatok, és egymástól függetlenül használja fel. Idővel ezek az adatok elavulnak, kikapcsolt, a konfigurálható adatmegőrzési időszak alapján. Ennek eredményeképpen egy adott eseményközpontban soha nem beolvasása "túl teljes."

Az Event Hubs Capture lehetővé teszi, hogy adja meg a saját Azure Blob storage-fiók és a tároló vagy az Azure Data Lake Store-fiókot, amely a rögzített adatok tárolására szolgálnak. Ezek a fiókok lehet az eseményközpont ugyanabban a régióban, vagy egy másik régióban, az Event Hubs rögzítés funkciója a rugalmas ad hozzá.

A rögzített adatrögzítés [Apache Avro][Apache Avro] formátumban történik: egy kompakt, gyors, bináris formátum, amely beágyazott sémával rendelkező, gazdag adatstruktúrákat biztosít. Ez a formátum széles körben használt, a Hadoop ökoszisztéma, a Stream Analytics és az Azure Data Factory. Avro használatával kapcsolatban további információt a cikk későbbi részében érhető el.

### <a name="capture-windowing"></a>Ablakkezelési rögzítése

Event Hubs rögzítés funkciójával szabályozhatja a rögzítés időszakok beállítása. Ebben az ablakban a minimális méret és időpontjának beállítása az "első wins házirendet," ami azt jelenti, hogy az észlelt első eseményindító hatására a rögzítési művelet. Ha egy 15 perc, 100 MB-os Rögzítés ablakban, és a méret ablakos eseményindítók előtt az időtartomány küldése 1 MB / másodperc. Mindegyik partíció egymástól függetlenül rögzíti, és ír egy befejezett blokkblob-készítés, az idő, neve, amelyen a rögzítési időköz történt a következő alkalommal. A tároló elnevezési egyezmény a következőképpen történik:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Vegye figyelembe, hogy a dátumértéket helyeken nullákat; lehet, hogy az a példában fájlnév:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Abban az esetben, ha az Azure Storage-blob átmenetileg nem érhető el, Event Hubs Capture megőrzi az adatait az Event hub-on beállított adatmegőrzési időszakra vonatkozóan, és a Storage-fiók újbóli rendelkezésre állása után ismét kitölti az adatok kitöltését.

### <a name="scaling-to-throughput-units"></a>Az átviteli egységek méretezése

Event Hubs forgalmat kezelnek [átviteli egységek](event-hubs-scalability.md#throughput-units). Egy átviteli egység lehetővé teszi, hogy 1 MB másodpercenként bejövő és kimenő forgalom kétszer adott mennyiségű második vagy 1000 esemény. A standard Event Hubs 1-20 átviteli egységekkel konfigurálható, és a kvóták növelésére vonatkozó [támogatási kéréssel][support request]többet is megvásárolhat. A megvásárolt átviteli egységek használat folyamatban van. Az Event Hubs Capture adatokat másol közvetlenül a belső az Event Hubs-tároló átviteli egység a kimenő forgalom kvóták kihagyásával, és a kimenő forgalom mentése más feldolgozási olvasók, például a Stream Analytics vagy a Spark.

Miután konfigurálta az Event Hubs Capture automatikusan fut, amikor az első eseményt küld, és továbbra is fut. Hogy egyszerűbb legyen az tudnia, hogy működik-e a folyamat az alárendelt feldolgozáshoz, az Event Hubs üres fájlok ír, amikor nem szerepel megjeleníthető adat. Ez a folyamat egy előre jelezhető-váltás gyakoriságáról és jelölő, amely képes a batch-processzorok biztosít.

## <a name="setting-up-event-hubs-capture"></a>Event Hubs rögzítés beállítása

Capture konfigurálható az event hub létrehozási idő az az [az Azure portal](https://portal.azure.com), vagy az Azure Resource Manager-sablonok használatával. További információkért tekintse át a következő cikkeket:

- [Engedélyezze az Event Hubs Capture az Azure portal használatával](event-hubs-capture-enable-through-portal.md)
- [Event Hubs-névtér létrehozása egy eseményközponttal és a rögzítés funkció engedélyezése az Azure Resource Manager-sablon használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>A rögzített fájlok vizsgálatával és az avro segítségével

Az Event Hubs Capture fájlokat hoz létre az Avro formátum beállított idő ablakban megadott. Ezeket a fájlokat bármely eszközön megtekintheti, például [Azure Storage Explorer][Azure Storage Explorer]. Letöltheti a fájlokat, és helyileg is dolgozhatnak.

Az Event Hubs Capture által előállított fájlok a következő Avro-sémában rendelkezik:

![Avro séma][3]

A Avro-fájlok felfedezésének egyszerű módja a [Avro Tools][Avro Tools] jar használata az Apache-ból. Az [Apache-részletezéssel][Apache Drill] egyszerűsített SQL-alapú élményt és [Apache Spark][Apache Spark] is használhat a betöltött adatok összetett elosztott feldolgozásának elvégzéséhez. 

### <a name="use-apache-drill"></a>Apache-részletezés használata

Az [Apache Drill][Apache Drill] egy "nyílt forráskódú SQL-lekérdezési motor a Big adatfeltáráshoz", amely strukturált és részben strukturált adatforrásokat képes lekérdezni, bárhol is legyenek. A motor a nagyszerű teljesítmény érdekében önálló csomópontként vagy hatalmas fürtként is futtatható.

Az Azure Blob Storage natív támogatása elérhető, amely megkönnyíti az adatlekérdezést egy Avro-fájlban a dokumentációban leírtak szerint:

[Apache-részletezés: Azure Blob Storage beépülő modul][Apache Drill: Azure Blob Storage Plugin]

A rögzített fájlok lekéréséhez létrehozhat és futtathat egy virtuális gépet, amely az Apache-részletezés használatával engedélyezhető egy tárolón keresztül az Azure Blob Storage eléréséhez:

https://github.com/yorek/apache-drill-azure-blob

Teljes körű minta érhető el a streaming on Scale repositoryban:

[Folyamatos átvitel a skálán: Event Hubs rögzítés]

### <a name="use-apache-spark"></a>Apache Spark használata

A [Apache Spark][Apache Spark] egy "egységes analitikai motor a nagyméretű adatfeldolgozáshoz". Más nyelveket is támogat, beleértve az SQL-t, és könnyen elérheti az Azure Blob Storage-t. Az Azure-ban két lehetőség van Apache Spark futtatására, és mindkettő egyszerű hozzáférést biztosít az Azure Blob Storage-hoz:

- [HDInsight: Címlisták az Azure Storage-ban][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob Storage][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Avro-eszközök használata

A [Avro-eszközök][Avro Tools] jar-csomagként érhetők el. A jar-fájl letöltése után a következő parancs futtatásával megtekintheti egy adott Avro-fájl sémáját:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
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

Az Avro-eszközök segítségével a fájl átalakítása JSON-formátumban, és végezze el a többi feldolgozó.

Speciális feldolgozást végez, töltse le és telepítse a avro-hoz tartozó platform a választott. A cikk írásának időpontjában érhetők el megvalósításokhoz C, C++, C\#, Java, NodeJS, Perl, a PHP, Python és Ruby.

Az Apache Avro teljes körű Első lépések útmutatók a [Javához][Java] és a [Pythonhoz][Python]. Is olvashatja a [Ismerkedés az Event Hubs Capture](event-hubs-capture-python.md) cikk.

## <a name="how-event-hubs-capture-is-charged"></a>Hogyan Event Hubs rögzítés díját

Az Event Hubs Capture forgalmi díjas hasonlóan az átviteli egységek: mint óradíjat számítunk fel. A díj arányos a névtérhez tartozó a megvásárolt átviteli egységek számát. Átviteli egységek növelhető és csökkenthető, az Event Hubs Capture mérőszámok növelheti és csökkentheti a megfelelő teljesítmény elérése érdekében. A mérőszámok párhuzamosan történik. A díjszabás részleteiért lásd: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). 

Vegye figyelembe, hogy a rögzítés nem használja fel a kimenő forgalomra vonatkozó kvótát, mivel azt külön számlázzák. 

## <a name="integration-with-event-grid"></a>Event Grid-integráció 

Létrehozhat Azure Event Grid-előfizetés az Event Hubs-névtér forrásként. A következő oktatóanyag bemutatja, hogyan hozhat létre Event Grid-előfizetést egy Event hub forrásként és egy Azure Functions alkalmazásként fogadóként: Rögzített Event Hubs-adatfeldolgozások [feldolgozása és migrálása egy SQL Data Warehouse Event Grid és Azure functions használatával](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>További lépések

Az Azure-bA az adatok a legegyszerűbb módja az Event Hubs Capture. Használja az Azure Data Lake, az Azure Data Factory és az Azure HDInsight, a kötegelt feldolgozás hajthat végre, és más elemzési ismerős eszközök és platformok szabadon használatával, bármilyen méretben van szüksége.

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Első lépések, küldése és fogadása események](event-hubs-dotnet-framework-getstarted-send.md)
* [Event Hubs – áttekintés][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.1.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Folyamatos átvitel a skálán: Event Hubs rögzítés]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture

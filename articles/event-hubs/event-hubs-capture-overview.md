---
title: Streamelési események rögzítése – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a Rögzítés funkcióról, amely lehetővé teszi az Azure Event Hubs-on keresztül streamelt események rögzítését.
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
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265011"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Események rögzítése az Azure-eseményközpontokon keresztül az Azure Blob Storage-ban vagy az Azure Data Lake Storage-ban
Az Azure Event Hubs lehetővé teszi, hogy automatikusan rögzítse a streamelési adatokat az Event Hubs-ban egy [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) vagy az Azure Data Lake Storage Gen 1 vagy Gen [2](https://azure.microsoft.com/services/data-lake-store/) fiókban, az idő- vagy méretintervallum megadásának nagyobb rugalmasságával. A rögzítés beállítása gyors, nincsenek adminisztratív költségek a futtatásához, és automatikusan méretezhető az Event Hubs [átviteli egységekkel.](event-hubs-scalability.md#throughput-units) Az Event Hubs Capture a legegyszerűbb módja a streamelési adatok Azure-ba való betöltésének, és lehetővé teszi, hogy az adatfeldolgozásra összpontosítson, ne pedig az adatrögzítésre.

Az Event Hubs Capture lehetővé teszi, hogy valós idejű és kötegalapú folyamatokat dolgozzon fel ugyanazon az adatfolyamon. Ez azt jelenti, hogy olyan megoldásokat hozhat létre, amelyek idővel az Ön igényeinek megfelelően növekednek. Akár kötegelt rendszereket épít ma, amelyek a jövőbeli valós idejű feldolgozásfelé irányulnak, vagy hatékony hidegútvonalat szeretne hozzáadni egy meglévő valós idejű megoldáshoz, az Event Hubs Capture megkönnyíti a streamelési adatokkal való munkát.


## <a name="how-event-hubs-capture-works"></a>Az Eseményközpontok rögzítése működése

Az Event Hubs egy idő-megőrzési tartós puffer telemetriai be- és, hasonlóan egy elosztott naplóhoz. Az Event Hubs méretezésének kulcsa a [particionált fogyasztói modell.](event-hubs-scalability.md#partitions) Minden partíció egy független adatszegmens, és egymástól függetlenül kerül felhasználásra. Idővel ezek az adatok korok ki, a konfigurálható megőrzési időszak alapján. Ennek eredményeképpen egy adott eseményközpont soha nem lesz "túl tele".

Az Event Hubs Capture lehetővé teszi, hogy saját Azure Blob-tárfiókot és -tárolót, vagy az Azure Data Lake Storage-fiókot adja meg, amely a rögzített adatok tárolására szolgál. Ezek a fiókok lehetnek ugyanabban a régióban, mint az eseményközpont vagy egy másik régióban, hozzátéve, hogy a rugalmasság az Event Hubs Capture funkció.

A rögzített adatok [Apache Avro][Apache Avro] formátumban íródott: kompakt, gyors, bináris formátum, amely gazdag adatstruktúrákat biztosít inline sémával. Ezt a formátumot széles körben használják a Hadoop-ökoszisztémában, a Stream Analytics-ben és az Azure Data Factoryban. Az Avro-val való munkáról a cikk későbbi részében olvashat bővebben.

### <a name="capture-windowing"></a>Ablakrögzítés rögzítése

Az Event Hubs Capture lehetővé teszi egy ablak beállítását a rögzítés vezérlésére. Ez az ablak egy minimális méret- és időkonfiguráció, amely "első nyer házirend", ami azt jelenti, hogy az első eseményindító hibát okoz rögzítési műveletet. Ha van egy tizenöt perces, 100 MB-os rögzítési ablaka, és másodpercenként 1 MB-ot küld, a méretablak az időablak előtt aktiválódik. Minden partíció egymástól függetlenül rögzíti, és egy befejezett blokkblobot ír a rögzítés időpontjában, amely et a rögzítési időköz találkozásának időpontjáról nevezték el. A tároló elnevezési konvenciója a következő:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Ne feledje, hogy a dátumértékek nullákkal vannak kitömve; egy példa fájlnév lehet:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Abban az esetben, ha az Azure storage blob átmenetileg nem érhető el, Event Hubs Capture megőrzi az adatokat az adatmegőrzési időszak konfigurált az eseményközpontban, és töltse ki az adatokat, amint a tárfiók ismét elérhetővé válik.

### <a name="scaling-to-throughput-units"></a>Méretezés átviteli egységekre

Az Event Hubs-forgalmat [átviteli egységek](event-hubs-scalability.md#throughput-units)szabályozzák. Egyetlen átviteli egység lehetővé teszi, hogy 1 MB másodpercenként vagy 1000 események másodpercenként a be- és kétszer annyi kimenő. A Standard Event Hubs 1–20 átviteli egységgel konfigurálható, és a kvótanöveléstámogatási [kérelemmel][support request]többet vásárolhat. A megvásárolt átviteli egységeken túli használat szabályozása van. Event Hubs A rögzítés közvetlenül a belső Event Hubs-tárolóból másolja az adatokat, megkerülve az átviteli egység kimenő kvótáit, és mentve a kimenő forgalom más feldolgozó olvasók, például a Stream Analytics vagy a Spark számára.

Konfigurálás után az Event Hubs Capture automatikusan elindul az első esemény elküldésekor, és folytatja a futást. Annak érdekében, hogy az alsóbb rétegbeli feldolgozás könnyebben tudja, hogy a folyamat működik, az Event Hubs üres fájlokat ír, ha nincs enek adatok. Ez a folyamat kiszámítható ütem- és jelölőt biztosít, amely képes a kötegelt processzorok táplálását.

## <a name="setting-up-event-hubs-capture"></a>Az Eseményközpontok rögzítésének beállítása

A rögzítés konfigurálható az eseményközpont létrehozási idején az [Azure Portalon,](https://portal.azure.com)vagy az Azure Resource Manager-sablonok használatával. További információkért tekintse át a következő cikkeket:

- [Az Event Hubs Capture engedélyezése az Azure Portal használatával](event-hubs-capture-enable-through-portal.md)
- [Event Hubs-névtér létrehozása egy eseményközponttal és a Rögzítés funkció engedélyezése az Azure Resource Manager-sablonjának használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>A rögzített fájlok felfedezése és az Avro-val való együttműködés

Az Event Hubs Capture a konfigurált időablakban megadott avro formátumban hoz létre fájlokat. Ezeket a fájlokat bármely eszközben megtekintheti, például az [Azure Storage Explorer ben.][Azure Storage Explorer] A fájlokat helyileg letöltheti, hogy működjön rajtuk.

Az Event Hubs Capture által létrehozott fájlok a következő Avro-sémával rendelkeznek:

![Avro séma][3]

Az Avro fájlok felfedezésének egyszerű módja az [Apache Avro Tools][Avro Tools] jar-jának használata. Az Apache [Drill][Apache Drill] segítségével egy könnyű SQL-vezérelt élmény, vagy [apache Spark][Apache Spark] összetett elosztott feldolgozás a bevitt adatok at. 

### <a name="use-apache-drill"></a>Az Apache drill használata

[Az Apache Drill][Apache Drill] egy "nyílt forráskódú SQL-lekérdezési motor a Big Data feltárásához", amely lekérdezheti a strukturált és félig strukturált adatokat, bárhol is legyenek. A motor önálló csomópontként vagy hatalmas fürtként is futtatható a nagy teljesítmény érdekében.

Az Azure Blob storage natív támogatása elérhető, amely megkönnyíti az adatok lekérdezését egy Avro-fájlban, a dokumentációban leírtak szerint:

[Apache drill: Azure Blob Storage beépülő modul][Apache Drill: Azure Blob Storage Plugin]

A rögzített fájlok egyszerű lekérdezéséhez létrehozhat és végrehajthat egy virtuális gépet, amelyen az Apache Drill egy tárolón keresztül engedélyezve van az Azure Blob storage eléréséhez:

https://github.com/yorek/apache-drill-azure-blob

A teljes körű minta elérhető a Streamelés scale tárházban:

[Streamelés méretarányosan: Eseményközpontok rögzítése]

### <a name="use-apache-spark"></a>Az Apache Spark használata

[Az Apache Spark][Apache Spark] egy "egységes elemzési motor a nagyméretű adatfeldolgozáshoz". Támogatja a különböző nyelveken, beleértve az SQL, és könnyen elérheti az Azure Blob storage. Az Azure-ban több lehetőség is kínálkodhet az Apache Spark futtatására, és mindegyik egyszerű hozzáférést biztosít az Azure Blob storage-hoz:

- [HDInsight: Címfájlok az Azure storage-ban][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob storage][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Az Avro Tools használata

[Avro Tools][Avro Tools] állnak rendelkezésre, mint egy jar csomagot. A jar fájl letöltése után egy adott Avro fájl sémája a következő parancs futtatásával látható:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Ez a parancs visszaad

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

Az Avro Tools segítségével a fájlt JSON formátumra konvertálhatja, és egyéb feldolgozást is végezhet.

A fejlettebb feldolgozás elvégzéséhez töltse le és telepítse az Avro-t az Ön által választott platformhoz. Ebben az időben az írás, vannak megvalósítások elérhető C, C ++, C\#, Java, NodeJS, Perl, PHP, Python, és Ruby.

Az Apache Avro teljes útmutatót készített a [Java][Java] és a [Python][Python]számára. Az [Event Hubs Capture első lépései](event-hubs-capture-python.md) című cikket is elolvashatja.

## <a name="how-event-hubs-capture-is-charged"></a>Az Eseményközpontok rögzítésének díja?

Az Eseményközpontok rögzítése az átviteli egységekhez hasonlóan történik: óránkénti töltésként. A díj közvetlenül arányos a névtérhez vásárolt átviteli egységek számával. Az átviteli egységek növelésével és csökkenésével az Eseményközpontok rögzítési mérőszámai növekednek és csökkennek, hogy megfelelő teljesítményt nyújtsanak. A mérők párhuzamosan fordulnak elő. Az árképzési részletekről az [Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/). 

Vegye figyelembe, hogy a Rögzítés nem használja fel a kimenő kvótákat, mivel a számlázás külön van számlázva. 

## <a name="integration-with-event-grid"></a>Integráció az eseményhálóval 

Azure Event Grid-előfizetést egy Event Hubs névtér rel a forrásával hozhat létre. Az alábbi oktatóanyag bemutatja, hogyan hozhat létre Event Grid-előfizetést egy eseményközponttal forrásként és egy Azure Functions alkalmazással fogadóként: [A rögzített Eseményközpontok adatainak feldolgozása és áttelepítése egy SQL Data Warehouse-ba az Event Grid és az Azure Functions használatával.](store-captured-data-data-warehouse.md)

## <a name="next-steps"></a>További lépések
Az Event Hubs Capture a legegyszerűbb módja az azure-ba való bekerülésnek. Az Azure Data Lake, az Azure Data Factory és az Azure HDInsight használatával a kötegelt feldolgozást és más elemzéseket az Ön által választott, megszokott eszközök és platformok használatával, bármilyen méretekben elvégezheti.

Megtudhatja, hogy miként engedélyezheti ezt a funkciót az Azure Portal és az Azure Resource Manager sablon használatával:

- [Az Event Hubs Capture engedélyezése az Azure Portal használatával](event-hubs-capture-enable-through-portal.md)
- [Az Eseményközpontok rögzítésének engedélyezése Azure Resource Manager-sablon használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streamelés méretarányosan: Eseményközpontok rögzítése]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture

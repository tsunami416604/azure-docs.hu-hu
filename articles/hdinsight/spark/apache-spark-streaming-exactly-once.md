---
title: A Spark Streaming feladatok pontosan létrehozása – egyszer esemény feldolgozása - Azure HDInsight |} Microsoft Docs
description: Hogyan állítható be a Spark Streaming dolgozni egy eseményt, egyszer és csak egyszer.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: 61162a845f6620d7c6e5662baedfdbfae9b9653a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>A Spark Streaming feladatok pontosan létrehozása – egyszer esemény feldolgozása

Az adatfolyam feldolgozása alkalmazások hogyan azokat újra feldolgozásra üzenetek kezeléséhez néhány meghibásodás után a rendszer hajtsa végre különböző szempontok:

* Legalább egyszer: minden üzenetet garantáltan feldolgozását, de az beszerzése feldolgozása esetleg egynél többször.
* Legfeljebb egyszer: Előfordulhat, hogy minden üzenetet, vagy nem dolgozható fel. Egy üzenet feldolgozása, akkor csak egyszer dolgozza fel.
* Pontosan egyszer: minden üzenetet garantáltan egyszer és csak egyszer lehet feldolgozni.

Ez a cikk bemutatja, hogyan konfigurálhatja a Spark Streaming pontosan eléréséhez-egyszer feldolgozása.

## <a name="exactly-once-semantics-with-spark-streaming"></a>Pontosan-egyszer a Spark Streaming szemantikájának módosítása

Először érdemes hogyan meghibásodási ponttal minden rendszer újraindítása után egy játékhoz, és hogyan adatvesztés elkerülése érdekében. A Spark Streaming alkalmazás rendelkezik:

* Bemeneti forrása
* A bemeneti forrás olvasnak be adatokat egy vagy több címzett folyamat
* Az adatok feldolgozása feladatokhoz
* Kimeneti fogadóként
* Egy illesztőprogram folyamat, amely a hosszan futó feladatot kezeli

Pontosan-szemantikáját szükséges, hogy adatok nem vesztek bármikor, és a üzenet feldolgozása újraindítható, függetlenül attól, ahol a hiba akkor fordul elő, ha.

### <a name="replayable-sources"></a>Replayable források

A Spark Streaming alkalmazás éppen olvas az események forrása lehet *replayable*. Ez azt jelenti, hogy olyan esetekben, amikor az üzenet be lett olvasva, de ezután a rendszer az előtt meghiúsult az üzenet nem megőrzött vagy feldolgozott, a forrás kell biztosított ugyanaz az üzenet újból.

Azure-ban az Azure Event Hubs és a hdinsight Kafka meg replayable források. Egy másik replayable forrás: HDFS, Azure Storage blobs, például a hibatűrő fájlrendszer, vagy az Azure Data Lake Store, ahol minden adat másolatok örökre és bármikor, újra olvassa el az adatok teljes egészében.

### <a name="reliable-receivers"></a>Megbízható fogadók

Spark Streaming, az adatforrásokat, mint az Event Hubs és a Kafka *megbízható fogadók*, ahol minden fogadó nyomon követi, hogy a telepítés előrehaladását a forrás olvasásakor. A megbízható fogadó állapotában a hibatűrő tárolóba, ZooKeeper belül vagy a HDFS írt Spark Streaming ellenőrzőpontokkal tartja fenn. Ha ilyen fogadó sikertelen lesz, és később újra kell indítani, azt is onnan folytathatja az adatgyűjtést, ahol abbahagyta.

### <a name="use-the-write-ahead-log"></a>Az írási előre napló használata

Spark Streaming támogatja az írási előre napló, ahol minden fogadott esemény először írni a Spark ellenőrzőpont könyvtárat a hibatűrő tárolási és ott a egy rugalmas elosztott Dataset (RDD). Az Azure a hibatűrő tároló nem a HDFS vagy az Azure Storage, vagy az Azure Data Lake Store által támogatott. Az Spark Streaming-alkalmazás az írási előre napló engedélyezve van az összes fogadó úgy, hogy a `spark.streaming.receiver.writeAheadLog.enable` a konfigurációs beállítás `true`. Az írási előre napló biztosít hibatűrést az illesztőprogram-és a végrehajtója hibák.

Worker feladatok futtatása az esemény adatok alapján minden RDD definition mindkét replikálja, és több Worker pontjain van. Ha egy feladat sikertelen lesz, mivel a dolgozó összeomlott is fut, a feladat újraindítja a, az az eseményadatok replikái egy másik feldolgozónak, így az esemény legyen adatvesztés.

### <a name="use-checkpoints-for-drivers"></a>Az ellenőrzőpontok használata illesztőprogramok

A feladat illesztőprogramok kell újraindítható. Az illesztőprogram, a Spark Streaming alkalmazást futtató lefagy, ha szükséges azt minden futó fogadók, feladatok és bármely RDDs esemény adatainak tárolásához. Ebben az esetben kell lennie a feladat előrehaladását mentéséhez, így később folytathatja. Ez úgy érhető el, ellenőrzőpontok használata a irányított aciklikus diagramhoz (DAG), rendszeres időközönként a hibatűrő tárolási DStream. A DAG-metaadatokban a konfiguráció létrehozásához az adatfolyam-továbbítási alkalmazást, a műveleteket, amelyek meghatározzák az alkalmazás és minden kötegek, amelyek a várólistára, de még nem fejeződött be. A metaadatok lehetővé teszi, hogy újra kell indítani az ellenőrzőpont-adatok sikertelen illesztőprogramot. Ha az illesztőprogram újraindul, azt, hogy magukat az adat-helyreállítást esemény újra üzembe a írási előre naplóból RDDs új fogadók elindul.

Az ellenőrzőpontok a Spark Streaming engedélyezve vannak a két lépésben. 

1. Adja meg a tárolási elérési útja az ellenőrzőpontok StreamingContext objektum:

    val ssc új StreamingContext (spark, Seconds(1)) ssc.checkpoint("/path/to/checkpoints") =

    A Hdinsightban ezeket az ellenőrzőpontokat menteni kell az alapértelmezett a fürthöz, vagy az Azure Storage, vagy az Azure Data Lake Store csatolt tárhelyen tárolnia.

2. Ezt követően adja meg a DStream a egy ellenőrzőpont időköz (másodpercben). A bemeneti esemény származó állapotadatokat bizonyos időközönként Storage megőrződjenek. Megőrzött adatok csökkentheti a szükséges a forrás-eseményből származó állapot újraépítésekor számítási.

    val lines = ssc.socketTextStream("hostname", 9999)  lines.checkpoint(30)  ssc.start()  ssc.awaitTermination()

### <a name="use-idempotent-sinks"></a>Az idempotent mosdók használata

A cél a fogadó, amelyre a projekt írja az eredmények tudja kezelni a helyzet, ahol t kap ugyanazt az eredményt csak egyszer kell lennie. Lehet, hogy a gyűjtő ilyen ismétlődő eredmények észleli, és figyelmen kívül hagyja azokat. Egy *idempotent* fogadó hívható többször nem állapotváltozás ugyanazokhoz az adatokhoz.

Az idempotent mosdók logika, amely először ellenőrzi, hogy létezik-e a bejövő okozza az adattárral implementálásával hozhat létre. Már létezik az eredmény, ha az írási megjelenjen-e a Spark feladat szempontjából sikeres, de valójában az adattároló figyelmen kívül hagyja az ismétlődő adatokat. Ha az eredmény nem létezik, majd a fogadó kell beszúrni új ennek tárolóval. 

Használhatja például a tárolt eljárás az Azure SQL Database, amely események szúr be egy tábla. Ez a tárolt eljárás először megkeresi az esemény által kulcsmezők, és csak akkor, ha nincsenek egyező esemény található a rekord a táblába beszúrt.

Egy másik példa, hogy az Azure Data Lake store vagy a particionált fájlrendszerben, például az Azure Storage-blobok. Ebben az esetben a fogadó logika nem kell egy fájl meglétének ellenőrzése. Ha az esemény képviselő a fájl létezik, egyszerűen felül tartozik ugyanazokhoz az adatokhoz. Ellenkező esetben egy új fájl jön létre a számított elérési úton.

## <a name="next-steps"></a>További lépések

* [Spark Streaming áttekintése](apache-spark-streaming-overview.md)
* [Magas rendelkezésre állású Spark Streaming feladatok a YARN létrehozása](apache-spark-streaming-high-availability.md)

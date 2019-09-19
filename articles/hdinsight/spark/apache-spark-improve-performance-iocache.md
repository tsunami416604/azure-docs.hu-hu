---
title: Apache Spark számítási feladatok teljesítménye az Azure HDInsight IO cache-sel (előzetes verzió)
description: Ismerje meg az Azure HDInsight IO cache-t, és hogyan használhatja a Apache Spark teljesítményének javítására.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: f3f171d4dfd3642dc71724afbe084c3fcbf8beaa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091071"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Apache Spark számítási feladatok teljesítményének javítása az Azure HDInsight IO cache használatával 

Az IO cache egy adatgyorsítótárazási szolgáltatás az Azure HDInsight, amely javítja Apache Spark feladatok teljesítményét. Az IO cache az [Apache TEZ](https://tez.apache.org/) és a [Apache Hive](https://hive.apache.org/) számítási feladatokhoz is használható, amelyek [Apache Spark](https://spark.apache.org/) fürtökön futtathatók. Az i/o-gyorsítótár egy RubiX nevű nyílt forráskódú gyorsítótárazási összetevőt használ. A RubiX egy helyi lemezes gyorsítótár, amely olyan big data elemzési motorokkal használható, amelyek a felhőalapú tárolási rendszerekből származó adatokhoz férnek hozzá. A RubiX egyediek a gyorsítótárazási rendszerek között, mivel SSD-meghajtókat használnak, és nem foglalják le az operációs memóriát gyorsítótárazási célokra. Az IO cache szolgáltatás elindítja és kezeli a RubiX metaadat-kiszolgálókat a fürt minden munkavégző csomópontján. Emellett a fürt összes szolgáltatását a RubiX cache transzparens használatára konfigurálja.

A legtöbb SSD több mint 1 GByte-t biztosít a sávszélesség másodpercenként. Ez a sávszélesség, amelyet az operációs rendszer memóriában tárolt fájljának gyorsítótára egészít ki, elegendő sávszélességet biztosít a big data számítási feldolgozó motorok, például a Apache Spark betöltéséhez. Az operációs memória elérhető marad a Apache Spark számára, hogy nagy mennyiségű memóriát lehessen feldolgozni, például a Shuffle-t. Az operációs memória kizárólagos használata lehetővé teszi, hogy a Apache Spark optimális erőforrás-használatot érjenek el.  

>[!Note]  
>Az IO cache jelenleg a RubiX-t használja gyorsítótárazási összetevőként, de ez a szolgáltatás jövőbeli verzióiban változhat. Használjon IO cache-interfészeket, és ne vegyen fel függőségeket közvetlenül a RubiX implementációján.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Az Azure HDInsight IO cache előnyei

Az i/o-gyorsítótár használata az Azure Blob Storageból beolvasott feladatok teljesítményének növelését teszi lehetővé.

Nem kell módosítania a Spark-feladatokat, hogy a teljesítmény megnövekszik az IO-gyorsítótár használatakor. Ha az IO-gyorsítótár le van tiltva, akkor ez a Spark-kód távolról is `spark.read.load('wasbs:///myfolder/data.parquet').count()`beolvashatja az adatait az Azure Blob Storage:. Ha az IO-gyorsítótár aktiválva van, akkor az IO-gyorsítótárból való beolvasás során ugyanez a kód okozza a gyorsítótárazást. A következő olvasások során az adatok helyileg olvashatók az SSD-ről. A HDInsight-fürtön futó munkavégző csomópontok helyileg csatlakoztatott, dedikált SSD-meghajtókkal vannak ellátva. A HDInsight IO cache ezeket a helyi SSD-ket használja a gyorsítótárazáshoz, ami a legalacsonyabb késést és maximalizálja a sávszélességet.

## <a name="getting-started"></a>Első lépések

Az Azure HDInsight IO gyorsítótára alapértelmezés szerint inaktiválva van az előzetes verzióban. Az i/o-gyorsítótár elérhető az Azure HDInsight 3.6 + Spark-fürtökön, amelyek Apache Spark 2,3-et futtatnak.  Az i/o-gyorsítótár aktiválásához tegye a következőket:

1. Válassza ki a HDInsight-fürtöt [a Azure Portalban](https://portal.azure.com).

1. Az **Áttekintés** oldalon (amely alapértelmezés szerint meg van nyitva a fürt kiválasztásakor) válassza a **Ambari Kezdőlap** elemet a **fürt irányítópultok**területen.

1. Válassza ki a bal oldali **i/o-gyorsítótár** szolgáltatást.

1. Válassza a **műveletek** és az **aktiválás**lehetőséget.

    ![Az i/o-gyorsítótár szolgáltatás engedélyezése a Ambari-ben](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Az i/o-gyorsítótár szolgáltatás engedélyezése a Ambari-ben")

1. Erősítse meg az összes érintett szolgáltatás újraindítását a fürtön.

>[!NOTE]  
> Annak ellenére, hogy az állapotjelző sáv aktiválva van, az i/o-gyorsítótár valójában nincs engedélyezve, amíg újra nem indítja a többi érintett szolgáltatást.

## <a name="troubleshooting"></a>Hibaelhárítás
  
Az IO-gyorsítótár engedélyezése után lemezterület-hibák merülhetnek fel a Spark-feladatok futtatásakor. Ezek a hibák azért fordulnak elő, mert a Spark helyi lemezes tárolást is használ az adatok tárolásához a csoszogó műveletek során. Az i/o-gyorsítótár engedélyezése után a Spark kifogyhat az SSD-ről, és a Spark Storage területe csökkent. Az i/o-gyorsítótár által használt terület mérete alapértelmezés szerint a teljes SSD-terület felére mutat. Az i/o-gyorsítótár lemezterület-használata a Ambari-ben konfigurálható. Ha lemezterület-hibákat kap, csökkentse az IO-gyorsítótárhoz használt SSD-terület mennyiségét, majd indítsa újra a szolgáltatást. Az i/o-gyorsítótárhoz beállított terület módosításához tegye a következő lépéseket:

1. Az Apache Ambari területen válassza ki a **HDFS** szolgáltatást a bal oldalon.

1. Válassza ki a **konfigurációkat** és a **speciális** lapokat.

    ![HDFS speciális konfigurációjának szerkesztése](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "HDFS speciális konfigurációjának szerkesztése")

1. Görgessen le, és bontsa ki az **Egyéni Core-site** terület elemet.

1. Keresse meg a **Hadoop. cache. adat. fullion. százalék**tulajdonságot.

1. Módosítsa az értéket a mezőben.

    ![I/o-gyorsítótár teljes százalékos értékének szerkesztése](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "I/o-gyorsítótár teljes százalékos értékének szerkesztése")

1. Kattintson a jobb felső sarokban található **Mentés** gombra.

1. Válassza az újraindítási**újraindítás minden érintett**elemet. > 

    ![Az összes érintett újraindítása](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Az összes érintett újraindítása")

1. Válassza **az összes újraindításának megerősítése**lehetőséget.

Ha ez nem működik, tiltsa le az IO cache-t.

## <a name="next-steps"></a>További lépések

- További információ az IO cache-ről, beleértve a teljesítménnyel kapcsolatos teljesítményteszteket ebben a blogbejegyzésben: [Apache Spark a feladatok akár 9x sebességgel HDInsight IO-gyorsítótárral](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)

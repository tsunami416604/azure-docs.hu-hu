---
title: Apache Spark teljesítménye – Azure HDInsight IO-gyorsítótár (előzetes verzió)
description: Ismerje meg az Azure HDInsight IO-gyorsítótárat, és azt, hogyan javíthatja azt az Apache Spark teljesítményének javítása érdekében.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 43875b87d26f144b85454077fd3c044c820132bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494986"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Az Azure HDInsight IO-gyorsítótár használatával javíthatja az Apache Spark-munkaterhelések teljesítményét

Az IO-gyorsítótár egy azure HDInsight adatgyorsítótárazási szolgáltatás, amely javítja az Apache Spark-feladatok teljesítményét. Az IO-gyorsítótár [apache TEZ](https://tez.apache.org/) és [Apache Hive](https://hive.apache.org/) számítási feladatokkal is működik, amelyek [Apache Spark-fürtökön](https://spark.apache.org/) futtathatók. Az IO-gyorsítótár egy RubiX nevű nyílt forráskódú gyorsítótárazási összetevőt használ. A RubiX egy helyi lemezgyorsítótár a felhőalapú tárolórendszerekből származó adatokhoz hozzáférő big data-elemző motorokkal. A RubiX egyedülálló a gyorsítótárazási rendszerek között, mivel szilárdtest-meghajtókat (SSD- ket) használ, nem pedig működési memóriát foglal le gyorsítótárazási célokra. Az IO cache szolgáltatás elindítja és kezeli a RubiX metaadat-kiszolgálókat a fürt minden munkavégző csomópontján. Azt is konfigurálja az összes szolgáltatást a fürt átlátható használata RubiX cache.

A legtöbb SSD másodpercenként több mint 1 GByte sávszélességet biztosít. Ez a sávszélesség, amelyet az operációs rendszer memórián belüli fájlgyorsítótára egészít ki, elegendő sávszélességet biztosít a big data számítási feldolgozó motorok, például az Apache Spark betöltéséhez. A működési memória az Apache Spark számára áll rendelkezésre az erősen memóriafüggő feladatok, például a véletlen sorrendű véletlen sorrendű lejátszás a folyamathoz. A működési memória kizárólagos használata lehetővé teszi az Apache Spark számára az optimális erőforrás-használat elérését.  

> [!Note]  
> Az IO cache jelenleg a RubiX-et használja gyorsítótárazási összetevőként, de ez változhat a szolgáltatás későbbi verzióiban. Használjon io-gyorsítótár-felületeket, és ne vegyen semmilyen függőséget közvetlenül a RubiX implementációtól.
>Az I/O-gyorsítótár jelenleg csak az Azure BLOB Storage támogatja.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Az Azure HDInsight I-gyorsítótár előnyei

Az I/O-gyorsítótár használata teljesítménynövekedést biztosít az Azure Blob Storage-ból adatokat beolvasni tartalmazó feladatok esetében.

Nem kell módosítania a Spark-feladatokat, hogy az IO-gyorsítótár használatakor a teljesítmény növekedéséhez. Ha az IO-gyorsítótár le van tiltva, ez a `spark.read.load('wasbs:///myfolder/data.parquet').count()`Spark-kód távolról olvassa be az adatokat az Azure Blob Storage-ból: . Az IO-gyorsítótár aktiválásakor ugyanaz a kódsor okoz gyorsítótárazott olvasást az IO-gyorsítótárban. A következő olvasások, az adatok helyileg olvasható SSD. A HDInsight-fürt munkavégző csomópontjai helyileg csatlakoztatott, dedikált SSD-meghajtókkal vannak felszerelve. A HDInsight IO-gyorsítótár ezeket a helyi SSD-ket használja a gyorsítótárazáshoz, ami a legalacsonyabb késleltetési szintet biztosítja, és maximalizálja a sávszélességet.

## <a name="getting-started"></a>Első lépések

Az Azure HDInsight IO-gyorsítótár alapértelmezés szerint inaktív az előzetes verzióban. Az IO-gyorsítótár az Apache Spark 2.3-at futtató Azure HDInsight 3.6+ Spark-fürtökön érhető el.  Az IO-gyorsítótár HDInsight 4.0-s rendszerének aktiválásához tegye a következőket:

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Válassza ki az **IO cache** szolgáltatást a bal oldalon.

1. Válassza a **Műveletek** **(Szolgáltatás műveletek** HDI 3.6-ban) lehetőséget, és **válassza az Aktiválás lehetőséget.**

    ![Az Io cache szolgáltatás engedélyezése ambariban](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Az Io cache szolgáltatás engedélyezése ambariban")

1. Erősítse meg a fürt összes érintett szolgáltatásának újraindítását.

> [!NOTE]  
> Annak ellenére, hogy a folyamatjelző jelzi az aktivált, io-gyorsítótár ténylegesen nem engedélyezve, amíg újra nem indítja a többi érintett szolgáltatások.

## <a name="troubleshooting"></a>Hibaelhárítás
  
Az I/O-gyorsítótár engedélyezése után lemezterület-hibák léphetnek fel a Spark-feladatok futtatásakor. Ezek a hibák azért fordulnak elő, mert a Spark helyi lemeztárolót is használ az adatok tárolására a keverési műveletek során. A Spark az I/O-gyorsítótár engedélyezése után elfogyhat az SSD-területből, és csökken a Spark-tároló kezezése. Az IO cache által használt terület a teljes SSD-terület fele. Az IO-gyorsítótár lemezterület-felhasználása konfigurálható az Ambari ban. Ha lemezterület-hibákat tapasztal, csökkentse az Io-gyorsítótár számára használt SSD-terület nagy részét, és indítsa újra a szolgáltatást. Az I/O-gyorsítótár térkészletének módosításához tegye a következő lépéseket:

1. Az Apache Ambari alkalmazásban válassza ki a bal oldali **HDFS** szolgáltatást.

1. Jelölje ki a **Configs** és **a Speciális** lapokat.

    ![HdFS speciális konfigurációszerkesztése](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "HdFS speciális konfigurációszerkesztése")

1. Görgessen le, és bontsa ki az **Egyéni magterület területet.**

1. Keresse meg a **hadoop.cache.data.fullness.percentage tulajdonságot.**

1. Módosítsa a mező értékét.

    ![Io-gyorsítótár teljességének szerkesztése százalék](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Io-gyorsítótár teljességének szerkesztése százalék")

1. Válassza a **Mentés** lehetőséget a jobb felső sarokban.

1. Válassza **az Újraindítás** > **újraindítása minden érintett lehetőséget.**

    ![Az Apache Ambari újraindítja az összes érintett](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Az összes érintett újraindítása")

1. Válassza **az Újraindítás megerősítése az összeset**lehetőséget.

Ha ez nem működik, tiltsa le az IO-gyorsítótárat.

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet az IO-gyorsítótárról, beleértve a teljesítménymutatókat ebben a blogbejegyzésben: [Az Apache Spark-feladatok akár 9x sebességgel is felgyorsulnak a HDInsight IO-gyorsítótárral](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)

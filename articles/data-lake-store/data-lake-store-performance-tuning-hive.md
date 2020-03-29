---
title: Az Azure Data Lake Storage Gen1 Hive teljesítményhangolási irányelvei | Microsoft dokumentumok
description: Az Azure Data Lake Storage Gen1 Hive teljesítményhangolási irányelvei
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61437276"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítményhangolási útmutató a Hive-hoz a HDInsight és az Azure Data Lake Storage Gen1 szolgáltatáshoz

Az alapértelmezett beállítások úgy vannak beállítva, hogy jó teljesítményt nyújtsanak számos különböző használati esetben.  I/O-igényes lekérdezések esetén a Hive behangolható, hogy jobb teljesítményt nyújtson az Azure Data Lake Storage Gen1 használatával.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen1 fiók**. Az azure [Data Lake Storage gen1](data-lake-store-get-started-portal.md) című témakörben talál útmutatást a létrehozásról.
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen1 fiókhoz. Lásd: [HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással című témakört.](data-lake-store-hdinsight-hadoop-use-portal.md) Győződjön meg arról, hogy engedélyezi a Távoli asztal szolgáltatást a fürtszámára.
* **A Hive futtatása a HDInsighton.**  A Hive-feladatok HDInsight-on való futtatásáról a [Hive használata a HDInsightban](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Teljesítményhangolási irányelvek a Data Lake Storage Gen1 szolgáltatáshoz.**  Az általános teljesítménykoncepciókról a [Data Lake Storage Gen1 teljesítményhangolási útmutatója című témakörben talál.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Paraméterek

Az alábbiakban a Data Lake Storage Gen1 teljesítményének javítása érdekében az alábbiak legfontosabb beállításait kell behangolni:

* **hive.tez.container.size** – az egyes feladatok által használt memória mennyisége

* **tez.grouping.min-méret** – az egyes leképezők minimális mérete

* **tez.grouping.max méretű** – az egyes leképezők maximális mérete

* **hive.exec.reducer.bytes.per.reducer** – az egyes szűkítők mérete

**hive.tez.container.size** – A tároló mérete határozza meg, hogy mennyi memória áll rendelkezésre az egyes feladatokhoz.  Ez a fő bemeneti az egyidejűség a Hive-ban.  

**tez.grouping.min-size** – Ez a paraméter lehetővé teszi az egyes leképezők minimális méretének beállítását.  Ha a Tez által választott leképezők száma kisebb, mint ennek a paraméternek az értéke, akkor Tez az itt megadott értéket fogja használni.

**tez.grouping.max-size** – A paraméter lehetővé teszi az egyes leképezők maximális méretének beállítását.  Ha a Tez által választott leképezők száma nagyobb, mint ennek a paraméternek az értéke, akkor Tez az itt beállított értéket fogja használni.

**hive.exec.reducer.bytes.per.reducer** – Ez a paraméter az egyes szűkítők méretét állítja be.  Alapértelmezés szerint minden szűkítő 256 MB.  

## <a name="guidance"></a>Útmutatás

**A hive.exec.reducer.bytes.per.reducer** beállítása – Az alapértelmezett érték jól működik, ha az adatok tömörítetlenek.  A tömörített adatok esetében csökkenteni e-k nélkűl kell csökkenteni a szűkítő méretét.  

**Állítsa be a hive.tez.container.size** – Minden csomópontban a memóriát a yarn.nodemanager.resource.memory-mb adja meg, és alapértelmezés szerint megfelelően kell beállítani a HDI-fürtön.  A megfelelő memória YARN-ban való beállításáról további információt ebben a [bejegyzésben](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom)talál.

Az I/O-igényes munkaterhelések nagyobb párhuzamosságelőnyeit élvezhetik a Tez-tároló méretének csökkentésével. Ez több tárolót ad a felhasználónak, ami növeli az egyidejűséget.  Egyes Hive-lekérdezések azonban jelentős mennyiségű memóriát igényelnek (pl. MapJoin).  Ha a feladat nem rendelkezik elegendő memóriával, futásközben memóriakiesést kap.  Ha kevés memóriával kell kivételét kapnia, akkor növelnie kell a memóriát.   

A futó feladatok vagy párhuzamosságok egyidejű számát a teljes YARN memória korlátozza.  A YARN-tárolók száma határozza meg, hogy hány egyidejű feladat futtatható.  A YARN memória csomópontonkénti megkereséséhez lépjen az Ambari térre.  Nyissa meg a YARN lapot, és tekintse meg a Configs lapot.  Ebben az ablakban megjelenik a YARN memória.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A Data Lake Storage Gen1 használatával a teljesítmény javítása kulcsa az egyidejűség lehető legnagyobb mértékű növelése.  A Tez automatikusan kiszámítja a létrehozandó feladatok számát, így nem kell beállítania.   

## <a name="example-calculation"></a>Példa számítása

Tegyük fel, hogy van egy 8 csomós D14-es fürtje.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Korlátozások

**Data Lake Storage Gen1 szabályozás** 

Ha eléri a Data Lake Storage Gen1 által biztosított sávszélesség-korlátokat, akkor a feladathibákat láthatja. Ez a feladatnaplókban lévő szabályozási hibák megfigyelésével azonosítható.  Csökkentheti a párhuzamosság növelésével Tez tároló méretét.  Ha több egyidejűségre van szüksége a munkájához, kérjük, vegye fel velünk a kapcsolatot.

Annak ellenőrzéséhez, hogy szabályozás alá kerül-e, engedélyeznie kell a hibakeresési naplózást az ügyféloldalon. Ezt a következőképpen teheti meg:

1. Helyezze a következő tulajdonságot a Hive config log4j tulajdonságaiba. Ez az Ambari nézetből végezhető el: log4j.logger.com.microsoft.azure.datalake.store=DEBUG A konfiguráció érvénybe lépéséhez az összes csomópont/szolgáltatás újraindítása.

2. Ha szabályozás alatt áll, a HTTP 429 hibakód jelenik meg a struktúra naplófájljában. A struktúranaplófájl a&lt;/tmp/&gt;user /hive.log könyvtárban található.

## <a name="further-information-on-hive-tuning"></a>További információ a Hive hangolásáról

Íme néhány blog, amely segít behangolni a Hive lekérdezések:
* [Hive-lekérdezések optimalizálása a Hadoop számára a HDInsightban](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Hive-lekérdezés teljesítményének hibaelhárítása](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [A HDInsight-on a Hive optimalizálása](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)

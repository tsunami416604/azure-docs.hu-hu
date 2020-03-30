---
title: 'Teljesítmény hangolása: Hive, HDInsight & Azure Data Lake Storage Gen2 | Microsoft dokumentumok'
description: Az Azure Data Lake Storage Gen2 Hive teljesítményhangolási irányelvei.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 66042568cede364c16302fbd85751de4113bbe0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327587"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Teljesítmény finomhangolása: Hive, HDInsight & Azure Data Lake Storage Gen2

Az alapértelmezett beállítások úgy vannak beállítva, hogy jó teljesítményt nyújtsanak számos különböző használati esetben.  I/O-igényes lekérdezések esetén a Hive behangolható, hogy jobb teljesítményt nyújtson az Azure Data Lake Storage Gen2 használatával.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen2 fiók**. Az egyik létrehozásáról a [rövid útmutató: Azure Data Lake Storage Gen2 tárfiók létrehozása](data-lake-storage-quickstart-create-account.md) című témakörben talál útmutatást.
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen2-fiókhoz. Lásd: [Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **A Hive futtatása a HDInsighton.**  A Hive-feladatok HDInsight-on való futtatásáról a [Hive használata a HDInsightban](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Teljesítményhangolási irányelvek a Data Lake Storage Gen2 szolgáltatáshoz.**  Az általános teljesítménykoncepciókról a [Data Lake Storage Gen2 teljesítményhangolási útmutatócímű témakörben talál.](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

Íme a data lake storage gen2 teljesítményének javítása érdekében a legfontosabb beállítások:

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
A Data Lake Storage Gen2 használatával a teljesítmény javítása kulcsa az egyidejűség lehető legnagyobb mértékű növelése.  A Tez automatikusan kiszámítja a létrehozandó feladatok számát, így nem kell beállítania.   

## <a name="example-calculation"></a>Példa számítása

Tegyük fel, hogy van egy 8 csomós D14-es fürtje.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>További információ a Hive hangolásáról

Íme néhány blog, amely segít behangolni a Hive lekérdezések:
* [Hive-lekérdezések optimalizálása a Hadoop számára a HDInsightban](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Hive-lekérdezés teljesítményének hibaelhárítása](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [A HDInsight-on a Hive optimalizálása](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)

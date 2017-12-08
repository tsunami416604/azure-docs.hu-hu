---
title: "Az Azure Data Lake Store Hive teljesítményének hangolása irányelvek |} Microsoft Docs"
description: "Az Azure Data Lake Store Hive teljesítményének hangolása irányelvek"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: e10bf8f7cbae2b81d22823ff74fe652c6bcb2da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Útmutatás a Hive HDInsight és az Azure Data Lake Store teljesítményhangolása

Az alapértelmezett beállításokat állították be, hogy a megfelelő teljesítmény számos különféle használati esetek biztosít.  I/o-igényes lekérdezések esetén a rendszer a Hive szabályozható ADLS és nagyobb teljesítményre van szüksége.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** a Data Lake Store-fiók eléréséhez. Lásd: [HDInsight-fürtök létrehozása a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy a fürt számára engedélyezi a távoli asztal.
* **HDInsight Hive futó**.  A HDInsight Hive-feladatok futtatásával kapcsolatos további tudnivalókért lásd: a [használata a HDInsight Hive] (https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-hive)
* **Teljesítményhangolás ADLS iránymutatást**.  Általános teljesítmény fogalmakat, lásd: [Data Lake Store teljesítmény hangolása útmutató](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Paraméterek

Az alábbiakban a továbbfejlesztett ADLS-teljesítmény hangolására legfontosabb beállítások:

* **Hive.tez.Container.size** – az egyes feladatok által használt memória mennyisége

* **tez.Grouping.min méretű** – minimális méret minden leképezője

* **tez.Grouping.max méretű** – maximális méret minden leképezője

* **Hive.Exec.reducer.bytes.per.reducer** – minden nyomáscsökkentő méret

**Hive.tez.Container.size** -tároló mérete határozza meg, hogy mennyi memória érhető el minden feladat esetében.  Ez az a fő bemeneti a Hive párhuzamossági vezérlése.  

**tez.Grouping.min méretű** – Ez a paraméter lehetővé teszi, hogy meg kell adnia minden leképező legkisebb méretét.  Ha, amely Tez kiválasztja mappers száma kisebb, mint ez a paraméter értékét, majd Tez fogja használni az itt beállított érték.  

**tez.Grouping.max méretű** – a paraméter lehetővé teszi, hogy meg kell adnia minden leképező maximális méretét.  Ha, amely Tez kiválasztja mappers száma nagyobb, mint ez a paraméter értékét, majd Tez fogja használni az itt beállított érték.  

**Hive.Exec.reducer.bytes.per.reducer** – Ez a paraméter minden nyomáscsökkentő méretét állítja be.  Alapértelmezés szerint minden nyomáscsökkentő mérete 256MB.  

## <a name="guidance"></a>Útmutatás

**Állítsa be a hive.exec.reducer.bytes.per.reducer** – az alapértelmezett érték az adatok tömörítetlen esetén is működik.  A tömörített adatok csökkentse a nyomáscsökkentő méretét.  

**Állítsa be a hive.tez.container.size** – minden csomóponton, a memória yarn.nodemanager.resource.memory MB-os által megadott és kell megfelelően beállítani a HDI-fürtnek alapértelmezés szerint.  A YARN a megfelelő memória beállításával kapcsolatos további információkért tekintse meg a [utáni](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

I/o-igényes munkaterhelések is kihasználhatja a további párhuzamossági Tez tároló méretének csökkentésével. Így a felhasználó további tárolókat, növelve a feldolgozási.  Bizonyos Hive-lekérdezések azonban jelentős mennyiségű memória (pl. MapJoin) szükséges.  Ha a feladat nem rendelkezik elég memóriával, memória kivétel futásidőben kívüli fog kapni.  Ha memória kivételek kívül, majd növelje a memória.   

A futó feladatok vagy párhuzamossági egyidejű száma a teljes YARN memória fog időpontjaihoz.  A YARN a tárolók száma szabja meg, hogy hány egyidejű feladatok futtathatók.  A YARN memória mennyisége megkereséséhez nyissa meg az Ambari.  A YARN és a Configs lapon.  A YARN memória ebben az ablakban jelenik meg.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A fontos ADLS teljesítményének javítása, hogy a lehető legrövidebb egyidejűségi növeléséhez.  Tez automatikusan számítja ki feladatokat, így nem kell állítsa be úgy kell létrehozni.   

## <a name="example-calculation"></a>Példa kiszámítása

Tegyük fel, egy 8 csomópont D14 fürt rendelkezik.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Korlátozások
**ADLS-szabályozás** 

UIf találati ADLS által biztosított sávszélesség határain, hogy feladat hibáihoz kezdenie. Ez azonosítható betartásával szabályozási hibák feladat naplókban által.  A párhuzamos végrehajtás Tez tároló méretének növelésével csökkenthető.  Ha a feladat több egyidejű van szüksége, lépjen kapcsolatba velünk a következő címen.   

Ha Ön első szabályozott ellenőrzéséhez szeretne engedélyezni a hibakeresési naplózás az ügyféloldalon. Ez hogyan azt teheti meg:

1. Helyezze el a következő tulajdonság a Hive-config log4j tulajdonságait. Ezt megteheti az Ambari nézetben: log4j.logger.com.microsoft.azure.datalake.store=DEBUG indítsa újra az összes a csomópontok/szolgáltatást a konfiguráció életbe léptetéséhez.

2. Ha Ön első szabályozott, látni fogja, a hive naplófájlban a HTTP 429 hibakód. A hive naplófájl van /tmp/&lt;felhasználói&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>További információ a Hive hangolása

Az alábbiakban néhány rendszerek, amelyek segítségével finomhangolhatják a Hive-lekérdezéseket:
* [A hdinsight Hadoop Hive-lekérdezések optimalizálása](https://azure.microsoft.com/en-us/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Hibaelhárítás a Hive-lekérdezések teljesítményét](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Az ignite előadás a HDInsight Hive a optimalizálása](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)

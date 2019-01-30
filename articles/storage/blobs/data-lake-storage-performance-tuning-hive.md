---
title: Az Azure Data Lake Storage Gen2 Hive teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen2 Hive teljesítmény-finomhangolási útmutató
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 07be781c917a466b67580a99490eca4f6ff29985
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239791"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Teljesítmény-finomhangolási útmutató a Hive a HDInsight és az Azure Data Lake Storage Gen2

Az alapértelmezett beállításokat, adja meg a megfelelő teljesítmény számos különböző használati helyzetekhez különböző állítani.  I/o-igényes lekérdezéseket, a Hive hangolásával jobb teljesítmény érdekében az Azure Data Lake Storage Gen2 beolvasásához.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen2 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [a rövid útmutató: Az Azure Data Lake Storage Gen2-storage-fiók létrehozása](data-lake-storage-quickstart-create-account.md)
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake Storage Gen2-fiókot.
* **HDInsight Hive futó**.  A HDInsight Hive-feladatok futtatásával kapcsolatos tudnivalókért lásd: [a HDInsight Hive használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Teljesítmény-finomhangolási útmutató a Data Lake Storage Gen2**.  Az általános teljesítmény fogalmak, lásd: [Data Lake Storage Gen2 teljesítményének hangolása útmutatója](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

Az alábbiakban a Data Lake Storage Gen2 jobb teljesítmény hangolására legfontosabb beállítások:

* **Hive.tez.Container.size** – az egyes feladatok által használt memória mennyisége

* **tez.Grouping.min méretű** – minimális méretét minden leképezője

* **tez.Grouping.max méretű** – maximális méretét minden leképezője

* **Hive.Exec.reducer.bytes.per.reducer** – minden nyomáscsökkentő méretét

**Hive.tez.Container.size** -tároló mérete határozza meg, mennyi memória érhető el minden egyes feladathoz.  Ez a fő bemeneti az egyidejűséget, a Hive szabályozása.  

**tez.Grouping.min méretű** – Ez a paraméter lehetővé teszi, hogy minden egyes eseményleképező minimális méretének beállítása.  Ha úgy dönt, Tez, leképező száma kisebb, mint ez a paraméter értékét, majd Tez az itt beállított értéket fogja használni.

**tez.Grouping.max méretű** – a paraméter lehetővé teszi, hogy minden egyes eseményleképező maximális méretének beállítása.  Ha leképező Tez úgy dönt, hogy a szám nagyobb, mint ez a paraméter értékét, majd Tez fogja használni az itt beállított érték.

**Hive.Exec.reducer.bytes.per.reducer** – Ez a paraméter beállítja az egyes nyomáscsökkentő méretét.  Alapértelmezés szerint minden nyomáscsökkentő érték 256MB.  

## <a name="guidance"></a>Útmutatás

**Állítsa be a hive.exec.reducer.bytes.per.reducer** – az alapértelmezett érték jól működik, ha az adatok tömörítetlen.  Tömörített adatok akkor csökkentse a nyomáscsökkentő méretét.  

**Állítsa be a hive.tez.container.size** – az egyes csomópontok memória yarn.nodemanager.resource.memory-mb-tal van megadva, és helyesen meg kell a HDI-fürt alapértelmezés szerint.  A YARN-ban a megfelelő memóriával kapcsolatos további információkért lásd: Ez [közzététele](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

I/o-igényű számítási feladatokhoz a Tez-tároló méretének csökkentésével további párhuzamosság is kihasználhatják. Ez lehetővé teszi a felhasználó több tároló, amely növeli a párhuzamosság.  Azonban bizonyos Hive-lekérdezések szükséges rendszer jelentős mennyiségű memóriát (pl. MapJoin).  Ha a feladat nem rendelkezik elegendő memóriával, futtatás közben memóriából fakadó kivétel kívüli kap.  Ha ki memória kivételeket kap, majd növelje a memória.   

A futó feladatok vagy párhuzamosság egyidejű számát, amelyet fog lehet a teljes YARN memória.  YARN-tárolók száma fogja diktálni hány egyidejű feladat futtatható.  A YARN-csomópontonkénti memória megkereséséhez nyissa meg az Ambari.  Keresse meg a YARN és a konfigurációkat lapon.  A YARN memória ebben az ablakban jelenik meg.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A kulcs használatával a Data Lake Storage Gen2 teljesítmény javításához, hogy a lehető legnagyobb mértékben egyidejűség mértékének növelése.  Tez automatikusan kiszámolja a feladatokat, amelyek kell létrehozni, így nem kell azt a számát.   

## <a name="example-calculation"></a>Példa kiszámítása

Tegyük fel, hogy egy 8 D14 fürtcsomópont-e meg.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>További információ a Hive-finomhangolás

Az alábbiakban néhány blogok, amelyek segítenek a Hive-Lekérdezések finomhangolása:
* [A HDInsight hadoop Hive-lekérdezések optimalizálása](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Hive a lekérdezési teljesítmény hibaelhárítása](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Az ignite-előadás Hive a HDInsight a optimalizálása](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)

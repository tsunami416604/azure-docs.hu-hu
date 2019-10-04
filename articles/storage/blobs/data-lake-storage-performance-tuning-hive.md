---
title: Azure Data Lake Storage Gen2 struktúra teljesítményének finomhangolására vonatkozó irányelvek | Microsoft Docs
description: Azure Data Lake Storage Gen2 struktúra teljesítményének finomhangolására vonatkozó irányelvek
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1290174fb87306b34be81ed7fa4fb5de3bfba43c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847126"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Teljesítmény-finomhangolási útmutató a HDInsight és Azure Data Lake Storage Gen2 struktúrához

Az alapértelmezett beállítások úgy lettek beállítva, hogy a jó teljesítményt nyújtsanak számos különböző használati esetben.  Az I/O-igényű lekérdezések esetében a kaptár beállítható úgy, hogy jobb teljesítményt kapjon a Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Data Lake Storage Gen2-fiók**. A létrehozásával kapcsolatos utasításokért lásd [: gyors útmutató: Azure Data Lake Storage Gen2 Storage-fiók létrehozása](data-lake-storage-quickstart-create-account.md)
* **Azure HDInsight-fürt** Data Lake Storage Gen2 fiókhoz való hozzáféréssel. Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) -fürtökkel
* **Struktúra futtatása a HDInsight-on**.  A kaptár-feladatok HDInsight való futtatásával kapcsolatos további információkért lásd: [a kaptár használata a HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Teljesítmény-finomhangolási irányelvek a Data Lake Storage Gen2**.  Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen2 teljesítmény-finomhangolási útmutató](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

A továbbfejlesztett Data Lake Storage Gen2 teljesítményének finomhangolásához a legfontosabb beállítások a következők:

* **kaptár. TEZ. Container. size** – az egyes feladatok által használt memória mennyisége

* **TEZ. grouping. min-size** – az egyes leképezések minimális mérete

* **TEZ. grouping. max-size** – az egyes leképezések maximális mérete

* **kaptár. exec. szűkítő. Bytes. per. szűkítő** – az egyes csökkentők mérete

**kaptár. TEZ. Container. size** – a tároló mérete határozza meg, hogy mennyi memória érhető el az egyes feladatokhoz.  Ez a fő bemenet a párhuzamosságok struktúrában való vezérléséhez.  

**TEZ. grouping. min-size** – ez a paraméter lehetővé teszi az egyes leképezések minimális méretének megadását.  Ha a TEZ által választott leképezések száma kisebb, mint a paraméter értéke, akkor a TEZ az itt beállított értéket fogja használni.

**TEZ. grouping. max-size** – a paraméter lehetővé teszi az egyes leképezések maximális méretének beállítását.  Ha a TEZ által választott leképezések száma nagyobb, mint a paraméter értéke, akkor a TEZ az itt beállított értéket fogja használni.

**kaptár. exec. szűkítő. Bytes. per. szűkítő** – ez a paraméter beállítja az egyes szűkítők méretét.  Alapértelmezés szerint minden egyes csökkentő 256MB.  

## <a name="guidance"></a>Útmutatás

A **kaptár. exec. szűkítő. Bytes. per. szűkítő beállítása** – az alapértelmezett érték jól működik, ha az adat kibontása nem történik meg.  A tömörített adatmennyiség csökkentése érdekében csökkentse a szűkítő méretét.  

A **kaptár. TEZ. Container. size beállítása** – az egyes csomópontokban a memóriát a következő szálak határozzák meg: fonal. nodemanager. Resource. Memory-MB, és a HDI-fürthöz alapértelmezés szerint helyesen kell beállítani.  A megfelelő memória a FONALban való beállításával kapcsolatos további információkért tekintse meg ezt a [bejegyzést](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Az I/O-igényes munkaterhelések a TEZ-tároló méretének csökkentésével több párhuzamosságot is igénybe vehetnek. Ez a felhasználónak több tárolót biztosít, ami növeli a párhuzamosságot.  Egyes kaptár-lekérdezések azonban jelentős mennyiségű memóriát igényelnek (például MapJoin).  Ha a feladatnak nincs elég memóriája, a rendszer a futtatókörnyezeten kívüli kivételt kap.  Ha nem rendelkezik a memóriával kapcsolatos kivételekkel, növelje a memóriát.   

A futó vagy párhuzamos feladatok egyidejű számát a rendszer a FONALak teljes memóriája alapján fogja megkötni.  A FONALas tárolók száma határozza meg, hogy hány egyidejű feladat futhat.  Ha a szál memóriáját szeretné megkeresni egy csomóponton, nyissa meg a Ambari.  Navigáljon a FONALhoz, és tekintse meg a konfigurációk lapot.  Ebben az ablakban a szál memóriája jelenik meg.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A Data Lake Storage Gen2 használatával javíthatja a teljesítményt, hogy a lehető legnagyobb mértékben növelje a párhuzamosságot.  A TEZ automatikusan kiszámítja a létrehozandó feladatok számát, így nem kell azt beállítania.   

## <a name="example-calculation"></a>Példa a számításra

Tegyük fel, hogy 8 csomópontos D14-fürtöt tartalmaz.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>További információ a kaptár hangolásáról

Itt talál néhány olyan blogot, amely segít a kaptár-lekérdezések hangolásában:
* [Kaptár-lekérdezések optimalizálása a Hadoop a HDInsight-ben](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [A kaptár lekérdezési teljesítményének hibaelhárítása](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite-előadás a kaptár optimalizálása a HDInsight-on](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)

---
title: Az Adatmásolás Azure Data Lake Storage Gen2 a DistCp használatával | Microsoft Docs
description: Adatok másolása Data Lake Storage Gen2ba és a DistCp eszköz használatával
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388169"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Az DistCp használata az Azure Storage-blobok és a Azure Data Lake Storage Gen2 közötti adatmásoláshoz

Az [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) használatával az általános célú v2 Storage-fiók és az általános célú v2 Storage-fiók közötti Adatmásolást engedélyezheti a hierarchikus névtérrel. Ez a cikk útmutatást nyújt a DistCp eszköz használatáról.

A DistCp számos parancssori paramétert biztosít, és nyomatékosan javasoljuk, hogy olvassa el ezt a cikket a használatának optimalizálása érdekében. Ez a cikk alapszintű funkciókat mutat be, miközben az adatoknak a hierarchikus névtérben engedélyezett fiókba való másolására koncentrál.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy meglévő Azure Storage-fiók, amelyhez nincs engedélyezve Data Lake Storage Gen2 képesség (hierarchikus névtér)** .
* **Egy Data Lake Storage Gen2 funkciót engedélyező Azure Storage-fiók**. A létrehozásával kapcsolatos utasításokért lásd: [Azure Data Lake Storage Gen2 Storage-fiók létrehozása](data-lake-storage-quickstart-create-account.md)
* A Storage-fiókban a hierarchikus névtérrel létrehozott **fájlrendszer** .
* **Azure HDInsight-fürt** hozzáférése Data Lake Storage Gen2 engedélyezve lévő Storage-fiókhoz. Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>DistCp használata egy HDInsight Linux-fürtből

An méretű HDInsight-fürthöz tartozik a DistCp segédprogram, amely az adatok különböző forrásokból egy HDInsight-fürtbe való másolására használható. Ha úgy állította be a HDInsight-fürtöt, hogy az Azure Blob Storaget használja, és Azure Data Lake Storage együtt, akkor a DistCp segédprogram azonnal használható az adatmásoláshoz is. Ebben a szakaszban a DistCp segédprogram használatát tekintjük át.

1. Hozzon létre egy SSH-munkamenetet a HDI-fürthöz. Lásd: [Kapcsolódás Linux-alapú HDInsight-fürthöz](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ellenőrizze, hogy el tudja-e érni meglévő általános célú v2-fiókját (a hierarchikus névtér engedélyezése nélkül).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A kimenetnek meg kell adnia a tároló tartalmának listáját.

3. Hasonlóképpen ellenőrizze, hogy elérhető-e a Storage-fiók a fürtön engedélyezett hierarchikus névtérrel. Futtassa az alábbi parancsot:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A kimenetnek meg kell adnia a Data Lake Storage fiókban található fájlok/mappák listáját.

4. A DistCp használatával másolhatja át a WASB adatait egy Data Lake Storage-fiókba.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    A parancs átmásolja a **/example/Data/Gutenberg/** mappa tartalmát a blob Storage-ban a Data Lake Storage fiókban lévő **/MyFolder** .

5. Hasonlóképpen, a DistCp használatával másolhatja át Data Lake Storage fiók adatait a Blob Storageba (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    A parancs átmásolja a **/MyFolder** tartalmát a Data Lake Store fiókban a WASB **/example/Data/Gutenberg/** mappájába.

## <a name="performance-considerations-while-using-distcp"></a>Teljesítménnyel kapcsolatos megfontolások a DistCp használata során

Mivel a DistCp legalacsonyabb részletessége egyetlen fájl, az egyidejű másolatok maximális számának beállítása a legfontosabb paraméter a Data Lake Storage optimalizálásához. Az egyidejű másolatok száma egyenlő a parancssorban található mappers (**m**) paraméterek számával. Ez a paraméter határozza meg az adatmásoláshoz használt leképezések maximális számát. Az alapértelmezett érték 20.

**Példa**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan határozza meg a használni kívánt leképezések számát?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: a "default" szál-alkalmazási várólista számára elérhető teljes memória meghatározása** – az első lépés az "alapértelmezett" fonal-alkalmazási várólista számára elérhető memória meghatározása. Ezek az információk a fürthöz társított Ambari-portálon érhetők el. Keresse meg a FONALat, és tekintse meg a konfigurációk lapot, és tekintse meg az "alapértelmezett" alkalmazás-várólista számára elérhető fonal-memóriát. Ez a teljes rendelkezésre álló memória a DistCp-feladatokhoz (amely valójában egy MapReduce-feladatokhoz).

* **2. lépés: a leképezések számának kiszámítása** – az **m** érték egyenlő a fonalas tároló méretének hányadosával. A FONALak tárolójának mérete információi a Ambari portálon is elérhetők. Navigáljon a FONALhoz, és tekintse meg a konfigurációk lapot. Ebben az ablakban a szál tárolójának mérete jelenik meg. A leképezések (**m**) számának megérkezésére szolgáló egyenlet

        m = (number of nodes * YARN memory for each node) / YARN container size

**Példa**

Tegyük fel, hogy egy 4x D14v2s-fürttel rendelkezik, és 10 TB-nyi adat átvitelét kísérli meg 10 különböző mappából. Az egyes mappák különböző mennyiségű adattal rendelkeznek, és az egyes mappákban lévő fájlméretek eltérőek.

* **Összes szál memóriája**: a Ambari-portálon azt állapítja meg, hogy a szál memóriája 96 GB D14-csomóponthoz. Tehát a négy csomópontos fürthöz tartozó összes szál memóriája a következő: 

        YARN memory = 4 * 96GB = 384GB

* **Hozzárendelések száma**: a Ambari-portálon azt állapítja meg, hogy a szál tárolójának mérete 3 072 MB a D14-fürt csomópontjaihoz. Így a leképezések száma:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Ha más alkalmazások használják a memóriát, akkor dönthet úgy, hogy csak a fürthöz tartozó fonal-memória egy részét használja a DistCp.

### <a name="copying-large-datasets"></a>Nagyméretű adatkészletek másolása

Ha az áthelyezni kívánt adatkészlet mérete nagy (például > 1 TB), vagy ha sok különböző mappával rendelkezik, érdemes lehet több DistCp-feladatot használni. Valószínűleg nincs teljesítménybeli nyereség, de kiterjeszti a feladatokat úgy, hogy ha bármilyen feladat meghiúsul, csak a teljes feladat helyett újra kell indítania az adott feladatot.

### <a name="limitations"></a>Korlátozások

* A DistCp olyan leképezéseket próbál létrehozni, amelyek mérete hasonló a teljesítmény optimalizálása érdekében. Előfordulhat, hogy a leképezések számának növelése nem mindig növeli a teljesítményt.

* A DistCp csak egy Mapper-fájlra korlátozódik. Ezért a fájlokhoz nem tartozhat több mapper. Mivel a DistCp csak egy leképezést rendelhet egy fájlhoz, ez korlátozza a nagy fájlok másolásához használható Egyidejűség mennyiségét.

* Ha kis számú nagyméretű fájllal rendelkezik, akkor a rendszer 256 MB-nyi adattömbökre osztja fel őket, hogy több potenciális párhuzamosságot biztosítson.

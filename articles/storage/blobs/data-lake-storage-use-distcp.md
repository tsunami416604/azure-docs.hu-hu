---
title: Adatok másolása az Azure Data Lake Storage Gen2 szolgáltatásba a DistCp használatával| Microsoft dokumentumok
description: Adatok másolása a Data Lake Storage Gen2-be és onnan a DistCp eszközzel
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255534"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Adatok másolása az Azure Storage Blobs és az Azure Data Lake Storage Gen2 között a DistCp használatával

A [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) segítségével adatokat másolhat egy általános célú V2 tárfiók és egy általános célú V2 tárfiók között, amelyen engedélyezve van a hierarchikus névtér. Ez a cikk a DistCp eszköz használatával kapcsolatos utasításokat tartalmaz.

A DistCp számos parancssori paramétert kínál, ezért javasoljuk, hogy olvassa el ezt a cikket annak érdekében, hogy optimalizálja a használatát. Ez a cikk alapvető funkciókat jelenít meg, miközben az adatok hierarchikus névtér-kompatibilis fiókba történő másolására összpontosít.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy meglévő Azure Storage-fiók a Data Lake Storage Gen2 képességei nélkül (hierarchikus névtér) engedélyezve van.**
* **Azure Storage-fiók, amelyen engedélyezve van a Data Lake Storage Gen2 funkció.** Az azure [data lake storage gen2 tárfiók létrehozása című](data-lake-storage-quickstart-create-account.md) témakörben talál útmutatást a létrehozásról.
* **Olyan fájlrendszer,** amelyet a hierarchikus névtérrel rendelkező tárfiókban hoztak létre.
* **Azure HDInsight-fürt,** amely a Data Lake Storage Gen2 használatával rendelkező tárfiókhoz való hozzáféréssel rendelkezik. Lásd: [Azure Data Lake Storage Gen2 használata Az Azure HDInsight-fürtökkel.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Győződjön meg arról, hogy engedélyezi a Távoli asztal szolgáltatást a fürtszámára.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>A DistCp használata HDInsight Linux-fürtről

A HDInsight-fürt a DistCp segédprogrammal érkezik, amellyel különböző forrásokból származó adatokat másolhat egy HDInsight-fürtbe. Ha úgy állította be a HDInsight-fürtöt, hogy együtt használja az Azure Blob Storage-t és az Azure Data Lake Storage-t, a DistCp segédprogram használható a beépített adatok másolásához is. Ebben a szakaszban a DistCp segédprogram használatát vizsgáljuk.

1. Hozzon létre egy SSH-munkamenetet a HDI-fürthöz. Lásd: [Csatlakozás Linux alapú HDInsight-fürthöz](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ellenőrizze, hogy el tudja-e érni a meglévő általános célú V2-fiókot (hierarchikus névtér engedélyezése nélkül).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A kimenetnek meg kell adnia a tároló ban lévő tartalom listáját.

3. Hasonlóképpen ellenőrizze, hogy a fürtből engedélyezett hierarchikus névtérrel elérhető tárfiókot is elérheti-e. Futtassa az alábbi parancsot:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A kimenetnek meg kell adnia a Data Lake Storage-fiókban lévő fájlok/mappák listáját.

4. A DistCp használatával adatokat másolhat a WASB-ból egy Data Lake Storage-fiókba.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    A parancs a Blob storage **/example/data/gutenberg/** mappájának tartalmát másolja a **/myfolder** mappába a Data Lake Storage fiókban.

5. Hasonlóképpen a DistCp használatával adatokat másolhat a Data Lake Storage-fiókból a Blob Storage (WASB) fiókba.

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    A parancs a **/myfolder** tartalmát a Data Lake Store-fiókban a **WASB /example/data/gutenberg/** mappájába másolja.

## <a name="performance-considerations-while-using-distcp"></a>Teljesítménybeli szempontok a DistCp használata közben

Mivel a DistCp legalacsonyabb részletessége egyetlen fájl, az egyidejű másolatok maximális számának beállítása a legfontosabb paraméter a Data Lake Storage-hoz való optimalizáláshoz. Az egyidejű másolatok száma megegyezik a parancssorban található**m)** paraméter számával. Ez a paraméter az adatok másolásához használt leképezők maximális számát adja meg. Az alapértelmezett érték 20.

**Példa**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan állapíthatom meg a használandó leképezők számát?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: Határozza meg az "alapértelmezett" YARN alkalmazásvárólistában rendelkezésre álló teljes memóriát** – Az első lépés az "alapértelmezett" YARN alkalmazásvárólista számára rendelkezésre álló memória meghatározása. Ez az információ a fürthöz társított Ambari portálon érhető el. Nyissa meg a YARN lapot, és tekintse meg a Configs fület, és tekintse meg az "alapértelmezett" alkalmazásvárólistában elérhető YARN memóriát. Ez a DistCp-feladat teljes rendelkezésre álló memóriája (ami valójában egy MapReduce feladat).

* **2. lépés: Számítsuk ki a leképezők számát** - Az **m** érték egyenlő a yarn memória és a YARN tároló méretének hányadosával. A YARN tároló méretére vonatkozó információk az Ambari portálon is elérhetők. Nyissa meg a YARN lapot, és tekintse meg a Configs lapot. Ebben az ablakban jelenik meg a YARN tároló mérete. A leképezők (**m**) számának

        m = (number of nodes * YARN memory for each node) / YARN container size

**Példa**

Tegyük fel, hogy van egy 4x D14v2s klaszter, és 10 TB adatot próbál átvinni 10 különböző mappából. Mindegyik mappa különböző mennyiségű adatot tartalmaz, és az egyes mappákon belüli fájlméretek eltérőek.

* **YarN-memória összesen:** Az Ambari portálról megállapítja, hogy a YARN memória 96 GB egy D14-csomópont esetében. Így a teljes YARN memória négy csomópontfürthöz: 

        YARN memory = 4 * 96GB = 384GB

* **Leképezők száma:** Az Ambari portálon megállapíthatja, hogy a YARN tároló mérete 3072 MB egy D14 fürtcsomópont esetében. Tehát a leképezők száma:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Ha más alkalmazások is használnak memóriát, akkor dönthet úgy, hogy csak a fürt YARN memóriájának egy részét használja a DistCp szolgáltatáshoz.

### <a name="copying-large-datasets"></a>Nagy adatkészletek másolása

Ha az áthelyezendő adatkészlet mérete nagy (például >1 TB), vagy ha sok különböző mappával rendelkezik, érdemes több DistCp-feladatot használnia. Valószínűleg nincs teljesítménynövekedés, de elosztja a feladatokat, így ha bármelyik feladat sikertelen, csak újra kell indítani a teljes feladat helyett.

### <a name="limitations"></a>Korlátozások

* A DistCp a teljesítmény optimalizálása érdekében hasonló méretű leképezőket próbál létrehozni. A leképezők számának növelése nem mindig növeli a teljesítményt.

* A DistCp fájlonként csak egy leképezőre korlátozódik. Ezért nem lehet több leképező, mint amennyi fájlja van. Mivel a DistCp csak egy leképezőt rendelhet egy fájlhoz, ez korlátozza a nagy fájlok másolásához használható egyidejűség mennyiségét.

* Ha kis számú nagy fájllal rendelkezik, akkor 256 MB-os fájltömbökre kell osztania őket, hogy több potenciális egyidejűséget biztosítson.

---
title: Adatok másolása az Azure Data Lake Storage Gen2 előzetes verziója a DistCp használata |} A Microsoft Docs
description: A DistCp eszközzel másolhat adatokat, és a Data Lake Storage Gen2 előzetes verzió
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 9c12f96399de218241c8aa7ed686113c17a7410c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244147"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2-preview"></a>Azure Storage-blobokat és az Azure Data Lake Storage Gen2 előzetes verzió közötti adatokat másolja a DistCp használatával

Használhat [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) másolhat adatokat egy általános célú V2-tárfiók és a egy általános célú V2-tárfiók között hierarchikus névtér esetében engedélyezve van. Ez a cikk útmutatást nyújt a DistCp eszközzel.

A DistCp biztosít különböző parancssori paraméterek, és Határozottan javasoljuk, hogy ez a cikk ahhoz, hogy azt a használat optimalizálásában. Ez a cikk bemutatja a alapvető funkciói közben történő adatmásolás engedélyezve hierarchikus névtér-fiókhoz való használatát kellene összpontosítani.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy meglévő Azure Storage-fiók nélkül a Data Lake Storage Gen2 előzetes verzióként elérhető funkciókat (hierarchikus névtér) engedélyezve**.
* **Azure Storage-fiókot a Data Lake Storage Gen2 (előzetes verzió) szolgáltatás engedélyezve van**. Létrehozásával kapcsolatos utasításokért lásd: [hozzon létre egy storage-fiókot az Azure Data Lake Storage Gen2 előzetes verzió](data-lake-storage-quickstart-create-account.md)
* **A fájlrendszer** , hogy létre lett hozva a tárfiókban lévő hierarchikus névtér engedélyezve van.
* **Az Azure HDInsight-fürt** együtt egy Data Lake Storage Gen2 engedélyezve van a tárfiókhoz való hozzáférést. Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](data-lake-storage-use-hdi-cluster.md). Ellenőrizze, hogy engedélyezi a távoli asztal a fürtöt.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>A DistCp használata a HDInsight Linux-fürt

A DistCp segédprogram, amely segítségével különböző forrásokból származó adatokat másolja egy HDInsight-fürt tartalmaz egy HDInsight-fürtön. A HDInsight-fürt az Azure Blob Storage és az Azure Data Lake Storage együttes használata van beállítva, ha a DistCp segédprogram lehet használatban-a-beépített is közötti adatokat másolja. Ebben a szakaszban áttekintjük, hogyan használható a DistCp segédprogramot.

1. Hozzon létre SSH-munkamenetből a HDI-fürt. Lásd: [csatlakozás Linux-alapú HDInsight-fürthöz](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ellenőrizze, hogy Ön hozzáférhessenek-e a meglévő általános célú V2 fiók (nélkül hierarchikus névtér engedélyezve van).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A kimenet kell biztosítania a tároló tartalmának listáját.

3. Ehhez hasonlóan ellenőrizze e képes-e a tárfiók eléréséhez hierarchikus névtér esetében engedélyezve van a fürtből. Futtassa az alábbi parancsot:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A kimenet a Data Lake Storage-fiókban lévő fájlok és mappák listáját kell biztosítania.

4. A DistCp használatával adatokat másol a WASB a Data Lake Storage-fiók.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    A parancs másolja a tartalmát a **/példa/data/gutenberg/** a Blob storage-mappát **/myfolder** a Data Lake Storage-fiókban.

5. Ehhez hasonlóan a DistCp használatával adatait átmásolhatja Data Lake-tárfiókra a Blob Storage (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    A parancs másolja a tartalmát **/myfolder** a Data Lake Store-fiókban való **/példa/data/gutenberg/** WASB-mappában.

## <a name="performance-considerations-while-using-distcp"></a>A DistCp használata közben a teljesítménnyel kapcsolatos szempontok

Mivel a DistCp a legalacsonyabb granularitási egyetlen fájlt, beállítás egyidejű példányok maximális száma az a legfontosabb paraméter, a Data Lake Storage elleni optimalizálhatja azt. Egyidejű példányszám leképező száma egyenlő (**m**) paraméter a parancssorban. Ez a paraméter leképező adatmásolás céljából használt maximális számát. Alapértelmezett értéke 20.

**Példa**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan állapítható meg a használandó leképező száma?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: Határozza meg a teljes memória érhető el a "default" YARN-alkalmazás várólistára** – az első lépés feladata annak megállapítása, a memória, a "default" YARN-alkalmazás várólistára. Ez az információ a fürthöz társított Ambari portálon érhető el. Keresse meg a YARN és a Configs lapon megtekintheti a YARN memória érhető el az "alapértelmezett" alkalmazás üzenetsorba. Ez az a teljes rendelkezésre álló memória a DistCp feladat (ez valójában egy MapReduce-feladatot).

* **2. lépés: Leképező számának kiszámítása** -értékét **m** egyenlő teljes YARN memória, a YARN-tároló mérete elosztva a hányadosa. A YARN tároló mérete információkat, valamint az Ambari-portálon érhető el. Keresse meg a YARN és a konfigurációkat lapon. A YARN tárolóméret ebben az ablakban jelenik meg. Ahhoz, hogy eljusson leképező száma a egyenlőségi (**m**) van

        m = (number of nodes * YARN memory for each node) / YARN container size

**Példa**

Tegyük fel, hogy rendelkezésére áll majd egy 4 x D14v2s fürt pedig 10 TB-nyi adat átvitele 10 különböző mappákat szeretne. A mappák mindegyike a különböző mennyiségű adatot tartalmaz, és a fájlméret belül különböznek.

* **Teljes memória YARN**: Az Ambari Portalról meghatározni, hogy a YARN memória 96 GB egy D14 csomópont. Így a YARN memória összesen négy csomópontot tartalmazó fürtben van: 

        YARN memory = 4 * 96GB = 384GB

* **Leképező száma**: Az Ambari Portalról meghatározni, hogy a YARN tárolóméret 3072 MB D14 fürtcsomópont esetén. Tehát van leképező száma:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Ha más alkalmazásokat használ a memóriát, majd lehet váltani, csak a fürt YARN memória egy részét a DistCp használják.

### <a name="copying-large-datasets"></a>Nagy mennyiségű adat másolása

Ha az adatkészlet áthelyezésének mérete nagy (például > 1 TB-ot), vagy ha több különböző mappa, akkor érdemes megfontolni a DistCp több feladatot. Valószínűleg nem nincs teljesítmény miatt, de terjeszti ki a feladatokat, hogy minden olyan feladat sikertelen lesz, ha csak a teljes feladat ahelyett, hogy az adott feladat újraindításához.

### <a name="limitations"></a>Korlátozások

* A DistCp megpróbálja létrehozni a teljesítmény optimalizálása érdekében mérete hasonló leképező. Leképező számának növelésével előfordulhat, hogy nem mindig növelheti teljesítményt.

* A DistCp fájlonként egyetlen eseményleképező korlátozódik. Így nem kell további leképező, mint a fájlokat. A DistCp csak rendelhet egy eseményleképező egy fájlt, mivel ez használható, nagy méretű fájlok másolása egyidejűségi mennyiségét korlátozza.

* Ha kevés nagyméretű fájlokat, majd akkor fel kell őket adattömbökbe 256 MB-os fájlt a lehetséges több egyidejűséget biztosít.

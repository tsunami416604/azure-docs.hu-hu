---
title: Adatok másolása az Azure Data Lake Storage Gen2 előzetes verziója a Distcp használata |} A Microsoft Docs
description: A Distcp eszközzel másolhat adatokat, és a Data Lake Storage Gen2 előzetes verzió
services: storage
author: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 065c4c4315bda209484cc1b2449980e55d4ac798
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522696"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Azure Storage-Blobok és a Data Lake Storage Gen2 előzetes verzió közötti adatokat másolja a Distcp használata

Ha egy HDInsight-fürtöt az Azure Data Lake Storage Gen2 előzetes verzió hozzáféréssel rendelkezik, használhatja a Hadoop-ökoszisztéma eszközökkel, mint például [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) adatmásolás céljából **a** egy HDInsight fürt storage (WASB) egy adatokat nyerhet ki Képes a Lake Storage Gen2-fiók. Ez a cikk útmutatást nyújt a Distcp eszközzel.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage (előzetes verzió) szolgáltatás engedélyezve van az Azure Storage-fiók**. Létrehozásával kapcsolatos utasításokért lásd: [hozzon létre egy storage-fiókot az Azure Data Lake Storage Gen2 előzetes verzió](quickstart-create-account.md)
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake-tárfiókra. Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](use-hdi-cluster.md). Ellenőrizze, hogy engedélyezi a távoli asztal a fürtöt.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>A Distcp használata a HDInsight Linux-fürt

A Distcp segédprogram, amely segítségével különböző forrásokból származó adatokat másolja egy HDInsight-fürt tartalmaz egy HDInsight-fürtön. A HDInsight-fürt az Azure Blob Storage és az Azure Data Lake Storage együttes használata van beállítva, ha a Distcp segédprogram lehet használatban-a-beépített is közötti adatokat másolja. Ebben a szakaszban áttekintjük, hogyan használható a Distcp segédprogramot.

1. Az asztalon SSH használatával csatlakozhat a fürthöz. Lásd: [csatlakozás Linux-alapú HDInsight-fürthöz](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Futtassa a parancsokat a SSH használatával.

2. Győződjön meg arról, hogy hozzáférhet-e az Azure Storage Blobs (WASB). Futtassa az alábbi parancsot:

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A kimenet kell biztosítania az storage blob tartalmának listáját.

3. Ehhez hasonlóan ellenőrizze, hogy elérheti a fürtből a Data Lake-tárfiókra. Futtassa az alábbi parancsot:

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A kimenet a Data Lake Storage-fiókban lévő fájlok és mappák listáját kell biztosítania.

4. A Distcp használatával adatokat másol a WASB a Data Lake Storage-fiók.

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    A parancs másolja a tartalmát a **/példa/data/gutenberg/** a Blob storage-mappát **/myfolder** a Data Lake Storage-fiókban.

5. Ehhez hasonlóan a Distcp használatával adatait átmásolhatja Data Lake-tárfiókra a Blob Storage (WASB).

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    A parancs másolja a tartalmát **/myfolder** a Data Lake Store-fiókban való **/példa/data/gutenberg/** WASB-mappában.

## <a name="performance-considerations-while-using-distcp"></a>A DistCp használata közben a teljesítménnyel kapcsolatos szempontok

Mivel a DistCp a legalacsonyabb granularitási egyetlen fájlt, beállítás egyidejű példányok maximális száma az a legfontosabb paraméter, a Data Lake Storage elleni optimalizálhatja azt. Egyidejű példányszám vezérli leképező számának beállítása (**m**) paraméter a parancssorban. Ez a paraméter leképező adatmásolás céljából használt maximális számát. Alapértelmezett értéke 20.

**Példa**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan állapítható meg a használandó leképező száma?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: Határozható meg a teljes YARN** – az első lépés az, hogy határozza meg a YARN a fürt, amelyen futtatja a DistCp feladat számára elérhető memória. Ez az információ a fürthöz társított Ambari portálon érhető el. Keresse meg a YARN és a Configs lapon, a YARN memória megtekintéséhez. A teljes YARN memória lekéréséhez szorozza meg a YARN-csomópontonkénti memória a csomópontok számát a fürt rendelkezik.

* **2. lépés: Leképező számának kiszámítása** -értékét **m** egyenlő teljes YARN memória, a YARN-tároló mérete elosztva a hányadosa. A YARN tároló mérete információkat, valamint az Ambari-portálon érhető el. Keresse meg a YARN és a konfigurációkat lapon. A YARN tárolóméret ebben az ablakban jelenik meg. Ahhoz, hogy eljusson leképező száma a egyenlőségi (**m**) van

        m = (number of nodes * YARN memory for each node) / YARN container size

**Példa**

Tegyük fel, hogy a fürt egy 4 D14v2s csomóponttal rendelkezik, és 10 TB-nyi adat átvitele 10 különböző mappákat szeretne. A mappák mindegyike a különböző mennyiségű adatot tartalmaz, és a fájlméret belül különböznek.

* **Teljes memória YARN**: meghatározni, hogy a YARN memória 96 GB egy D14 csomópont az Ambari-portálon. Így a YARN memória összesen négy csomópontot tartalmazó fürtben van: 

        YARN memory = 4 * 96GB = 384GB

* **Leképező száma**: meghatározni, hogy a YARN tárolóméret 3072 D14 fürtcsomópont esetén az Ambari-portálon. Tehát van leképező száma:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Ha más alkalmazásokat használ a memóriát, majd lehet váltani, csak a fürt YARN memória egy részét a DistCp használják.

### <a name="copying-large-datasets"></a>Nagy mennyiségű adat másolása

Ha az adatkészlet áthelyezésének mérete nagy (például > 1 TB-ot), vagy ha több különböző mappa, akkor érdemes megfontolni a DistCp több feladatot. Valószínűleg nem nincs teljesítmény miatt, de terjeszti ki a feladatokat, hogy minden olyan feladat sikertelen lesz, ha csak a teljes feladat ahelyett, hogy az adott feladat újraindításához.

### <a name="limitations"></a>Korlátozások

* A DistCp megpróbálja létrehozni a teljesítmény optimalizálása érdekében mérete hasonló leképező. Leképező számának növelésével előfordulhat, hogy nem mindig növelheti teljesítményt.

* A DistCp fájlonként egyetlen eseményleképező korlátozódik. Így nem kell további leképező, mint a fájlokat. A DistCp csak rendelhet egy eseményleképező egy fájlt, mivel ez használható, nagy méretű fájlok másolása egyidejűségi mennyiségét korlátozza.

* Ha kevés nagyméretű fájlokat, majd akkor fel kell őket adattömbökbe 256 MB-os fájlt a lehetséges több egyidejűséget biztosít. 

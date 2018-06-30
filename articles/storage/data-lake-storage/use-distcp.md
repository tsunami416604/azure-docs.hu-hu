---
title: Azure Data Lake tárolási Gen2 előzetes ból a Distcp használatával adatait átmásolhatja |} Microsoft Docs
description: Másolja az adatokat, és onnan Data Lake tárolási Gen2 előzetes ból a Distcp eszközzel
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 073d81baca7e174872806301236f547329836c45
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113476"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Azure Storage Blobs és a Data Lake tárolási Gen2 előzetes adatok másolása ból a Distcp használatával

Ha egy HDInsight-fürt hozzáférés az Azure Data Lake tárolási Gen2 előzetes, használhatja például a Hadoop-ökoszisztémával eszközök [ból a Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) adatok másolása **a** egy HDInsight fürt storage (WASB) egy adatokká Lake tárolási Gen2 képes a fiókot. Ez a cikk nyújt útmutatást a ból a Distcp eszközzel.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage (előzetes verzió) szolgáltatás engedélyezve van az Azure Storage-fiók**. Hogyan hozhat létre ilyet, lásd: [egy Azure Data Lake tárolási Gen2 előzetes storage-fiók létrehozása](quickstart-create-account.md)
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake-tárfiókra. Lásd: [használata Azure Data Lake tárolási Gen2 Azure HDInsight-fürtök](use-hdi-cluster.md). Győződjön meg arról, hogy a fürt számára engedélyezi a távoli asztal.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux fürtök ból a Distcp használata

HDInsight-fürtök a ból a Distcp segédprogramot, amely segítségével különböző forrásokból származó adatok másolása HDInsight-fürtöt tartalmaz. Ha konfigurálta az Azure Blob Storage és az Azure Data Lake Storage együttes használata a HDInsight-fürthöz, a ból a Distcp segédprogram használt, a-kész közötti másolásához is lehet. Ez a szakasz azt tekintse meg a ból a Distcp segédprogram használata.

1. Az asztalról a SSH parancs használatával csatlakozzon a fürthöz. Lásd: [csatlakozás egy Linux-alapú HDInsight-fürt](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Futtassa a parancsokat az SSH-kérés.

2. Győződjön meg arról, hogy az Azure Storage Blobs (WASB) végezheti el. Futtassa az alábbi parancsot:

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A kimeneti biztosítania kell a tárolási blob tartalmának listáját.

3. Ehhez hasonlóan ellenőrizze, hogy végezheti el a Data Lake-tárfiókra a fürtből. Futtassa az alábbi parancsot:

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A kimeneti fájlokat vagy mappákat a Data Lake-tárfiókra listáját kell biztosítania.

4. Ból a Distcp segítségével adatokat másolni WASB egy Data Lake-tárfiókra.

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    A parancs átmásolja a tartalmát a **/példa/data/gutenberg/** mappa a Blob storage **/myfolder** a a Data Lake-tárfiókra.

5. Ehhez hasonlóan az ból a Distcp adatainak másolhatja Data Lake-tárfiókra a Blob Storage (WASB).

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    A parancs átmásolja a tartalmát **/myfolder** a Data Lake Store-fiók **/példa/data/gutenberg/** WASB mappájában.

## <a name="performance-considerations-while-using-distcp"></a>Teljesítménnyel kapcsolatos szempontok ból a DistCp használata során

Mivel a legalacsonyabb granularitási ból a DistCp meg egyetlen fájl, a legfontosabb paraméter optimalizálása, szemben a Data Lake tárolási egyidejű példányok maximális száma. Egyidejű példányszám vezérli mappers számának beállítása (**m**) paraméter a parancssorban. Ez a paraméter-adatok másolása használt mappers maximális számát. Alapértelmezett érték 20.

**Példa**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan állapítható meg a használandó mappers száma?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: A teljes YARN memória meghatározása** – az első lépés az, hogy határozza meg a YARN memória az a ból a DistCp feladat futtató fürtre. Ez az információ az Ambari portálon a fürthöz tartozó érhető el. A YARN és a Configs lapon tekintse meg a YARN memória. Ahhoz, hogy a teljes YARN memória, szorozza meg a YARN csomópontonkénti memória a csomópontok számát a fürt rendelkezik.

* **2. lépés: Mappers számának kiszámítása** -értékének **m** megegyezik a YARN tároló mérete elosztva teljes YARN memória hányadosa. A YARN tároló mérete információ áll rendelkezésre, valamint az Ambari portálon. A YARN és a Configs lapon. A YARN tároló mérete ebben az ablakban jelenik meg. A kiszolgálófarmban lévő mappers száma összefüggést (**m**) van

        m = (number of nodes * YARN memory for each node) / YARN container size

**Példa**

Tegyük fel, hogy a fürt rendelkezik egy 4 D14v2s csomópontok pedig 10 TB-nyi adat átvitele 10 különböző mappák kívánt. Mappánkénti változó mennyiségű adatot tartalmaz, és a fájlméret, minden egyes mappákban lévő eltérőek.

* **Összes YARN memória**: az Ambari portal annak meghatározását, hogy-e a YARN memória 96 GB egy D14 csomópont. Igen négy csomópontos a fürt teljes YARN memória van: 

        YARN memory = 4 * 96GB = 384GB

* **Mappers száma**: az Ambari portal annak meghatározását, hogy a YARN tároló mérete 3072 D14 fürtcsomópont esetén. Igen van mappers száma:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Ha más alkalmazásokat memóriát használ, akkor választhatja használandó csak egy részét a fürt YARN memória ból a DistCp.

### <a name="copying-large-datasets"></a>Nagy adatkészletek másolása

Ha a dataset áthelyezésének mérete nagy (például > 1 TB-os), vagy ha több különböző mappa, érdemes lehet használni több ból a DistCp feladat. Valószínűleg nincs nincs jobb teljesítménye, de hogy terjeszti ki a feladatot, hogy a feladat sikertelen lesz, ha csak kell, hogy adott feladat, hanem a teljes feladat újraindításához.

### <a name="limitations"></a>Korlátozások

* Ból a DistCp megpróbál létrehozni mappers hasonló-nál, teljesítményének optimalizálása érdekében. Növelje meg a mappers nem mindig növelheti a teljesítményt.

* Ból a DistCp fájlonként csak egy leképező korlátozódik. Emiatt nem kell további mappers, mint a fájlokat. Ból a DistCp hozzárendelhet egy leképező fájlba, mert ez megoldással csökkenthető a feldolgozási nagy fájlok másolása használható.

* Ha kis számú nagyméretű fájlokat, majd meg kell bontva 256 MB fájl adattömbök további lehetséges egyidejűségi Önnek. 

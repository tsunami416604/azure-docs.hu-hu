---
title: "Adatait átmásolja WASB érkező vagy oda irányuló Data Lake Store használatának ból a Distcp |} Microsoft Docs"
description: "Adatok másolása és az Azure Storage Blobs Data Lake Store-ból a Distcp eszközzel"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 1c9e100b4a0e7781f0782a49835d50492895ded1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Adatmásolás az Azure Storage-blobok és a Data Lake Store között a DistCp-vel
> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Ha egy HDInsight-fürt hozzáféréssel rendelkező Data Lake Store-ba, segítségével például ból a Distcp Hadoop-ökoszisztémával eszközök adatok másolása **a** egy HDInsight fürt storage (WASB) a Data Lake Store-fiók. Ez a cikk nyújt útmutatást a ból a Distcp eszközzel.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** a Data Lake Store-fiók eléréséhez. Lásd: [HDInsight-fürtök létrehozása a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy a fürt számára engedélyezi a távoli asztal.

## <a name="do-you-learn-fast-with-videos"></a>Gyorsan tanul videók segítségével?
[A videó](https://mix.office.com/watch/1liuojvdx6sie) az adatok Azure Storage Blobs és a Data Lake Store használatának ból a DistCp közötti másolása.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux fürtök ból a Distcp használata

HDInsight-fürtök a ból a Distcp segédprogramot, amely segítségével különböző forrásokból származó adatok másolása HDInsight-fürtöt tartalmaz. Ha a HDInsight-fürtöt használja a Data Lake Store további tárolására van beállítva, a ból a Distcp segédprogram lehet használt, a-kész adatok másolása, illetve onnan, valamint egy Data Lake Store-fiókot. Ez a szakasz azt tekintse meg a ból a Distcp segédprogram használata.

1. Az asztalról a SSH parancs használatával csatlakozzon a fürthöz. Lásd: [csatlakozás egy Linux-alapú HDInsight-fürt](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Futtassa a parancsokat az SSH-kérés.

2. Győződjön meg arról, hogy az Azure Storage Blobs (WASB) végezheti el. Futtassa az alábbi parancsot:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    A kimeneti biztosítania kell a tárolási blob tartalmának listáját.

3. Ehhez hasonlóan ellenőrizze, hogy a Data Lake Store-fiók hozzáférhet a fürtből. Futtassa az alábbi parancsot:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    A kimeneti fájlok és mappák a Data Lake Store-fiókban listáját kell biztosítania.

4. Az ból a Distcp WASB a Data Lake Store-fiók adatainak másolhatja.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    A parancs átmásolja a tartalmát a **/példa/data/gutenberg/** a WASB mappájában **/myfolder** a Data Lake Store-fiókban.

5. Ehhez hasonlóan ból a Distcp segítségével WASB Data Lake Store-fiók adatainak másolása.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    A parancs átmásolja a tartalmát **/myfolder** a Data Lake Store-fiók **/példa/data/gutenberg/** WASB mappájában.

## <a name="performance-considerations-while-using-distcp"></a>Teljesítménnyel kapcsolatos szempontok ból a DistCp használata során

Mivel a legalacsonyabb granularitási ból a DistCp meg egyetlen fájl, a legfontosabb paraméter optimalizálása, szemben a Data Lake Store egyidejű példányok maximális száma. Egyidejű példányszám vezérli mappers számának beállítása (a perceké 'M ') paraméter a parancssorban. Ez a paraméter-adatok másolása használt mappers maximális számát. Alapértelmezett érték 20.

**Példa**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan állapítható meg a használandó mappers száma?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: A teljes YARN memória meghatározása** – az első lépés az, hogy határozza meg a YARN memória az a ból a DistCp feladat futtató fürtre. Ez az információ az Ambari portálon a fürthöz tartozó érhető el. A YARN és a Configs lapon tekintse meg a YARN memória. Ahhoz, hogy a teljes YARN memória, szorozza meg a YARN csomópontonkénti memória a csomópontok számát a fürt rendelkezik.

* **2. lépés: Mappers számának kiszámítása** -értékének **m** megegyezik a YARN tároló mérete elosztva teljes YARN memória hányadosa. A YARN tároló mérete információ áll rendelkezésre, valamint az Ambari portálon. A YARN és a Configs lapon. A YARN tároló mérete ebben az ablakban jelenik meg. A kiszolgálófarmban lévő mappers száma összefüggést (**m**) van

        m = (number of nodes * YARN memory for each node) / YARN container size

**Példa**

Tegyük fel, hogy a fürt rendelkezik egy 4 D14v2s csomópontok pedig 10 TB-nyi adat átvitele 10 különböző mappák kívánt. Mappánkénti változó mennyiségű adatot tartalmaz, és a fájlméret, minden egyes mappákban lévő eltérőek.

* Összes YARN memória - az Ambari portal annak meghatározását, hogy-e a YARN memória 96 GB egy D14 csomópont. Igen négy csomópontos a fürt teljes YARN memória van: 

        YARN memory = 4 * 96GB = 384GB

* Ennyi mappers - az Ambari portal annak meghatározását, hogy a YARN tároló mérete 3072 D14 fürtcsomópont esetén. Igen van mappers száma:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Ha más alkalmazásokat memóriát használ, akkor választhatja használandó csak egy részét a fürt YARN memória ból a DistCp.

### <a name="copying-large-datasets"></a>Nagy adatkészletek másolása

Ha a dataset áthelyezésének mérete nagy (például > 1 TB-os), vagy ha több különböző mappa, érdemes lehet használni több ból a DistCp feladat. Valószínűleg nincs nincs jobb teljesítménye, de hogy terjeszti ki a feladatot, hogy a feladat sikertelen lesz, ha csak kell, hogy adott feladat, hanem a teljes feladat újraindításához.

### <a name="limitations"></a>Korlátozások

* Ból a DistCp megpróbál létrehozni mappers hasonló-nál, teljesítményének optimalizálása érdekében. Növelje meg a mappers nem mindig növelheti a teljesítményt.

* Ból a DistCp fájlonként csak egy leképező korlátozódik. Emiatt nem kell további mappers, mint a fájlokat. Ból a DistCp hozzárendelhet egy leképező fájlba, mert ez megoldással csökkenthető a feldolgozási nagy fájlok másolása használható.

* Ha kis számú nagyméretű fájlokat, majd meg kell bontva 256 MB fájl adattömbök további lehetséges egyidejűségi Önnek. 
 
* Az Azure Blob Storage-fiók másolása, a másolási feladat lehet, hogy szabályozva a blob storage oldalon. Ez csökkenti a teljesítményt, a másolat. Az Azure Blob Storage korlátai által megszabott kapcsolatos további információkért lásd: Azure Storage-korlátok, [Azure-előfizetés és a szolgáltatásra vonatkozó korlátozások](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Lásd még:
* [Adatok másolása az Azure Storage Blobs Data Lake Store-bA](data-lake-store-copy-data-azure-storage-blob.md)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)

---
title: Adatok másolása, illetve onnan a Distcp használata az Azure Data Lake Storage Gen1 WASB |} A Microsoft Docs
description: A Distcp eszközzel másolhat adatokat, és az Azure Storage-Blobokból az Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 5c3f393278d3d51dd3a53cd19335cd242c15c6d8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58168707"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage-blobokat és az Azure Data Lake Storage Gen1 között a Distcp használatával
> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Ha egy HDInsight-fürtöt az Azure Data Lake Storage Gen1 hozzáféréssel rendelkezik, használhatja az adatok másolásához Hadoop-ökoszisztéma eszközökkel, mint például a Distcp **a** egy HDInsight fürt storage (WASB) egy Data Lake Storage Gen1 fiókba. Ez a cikk útmutatást nyújt a Distcp eszközzel.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Data Lake Storage Gen1 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake Storage Gen1 fiókot. Lásd: [egy HDInsight-fürt létrehozása a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ellenőrizze, hogy engedélyezi a távoli asztal a fürtöt.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>A Distcp használata a HDInsight Linux-fürt

A Distcp segédprogram, amely segítségével különböző forrásokból származó adatokat másolja egy HDInsight-fürt tartalmaz egy HDInsight-fürtön. Ha konfigurálta a HDInsight-fürt használata a Data Lake Storage Gen1 kiegészítő tárolóként, a Distcp segédprogram használt-a-beépített másolhat adatokat, és a egy Data Lake Storage Gen1 fiókjához, továbbá az is lehet. Ebben a szakaszban áttekintjük, hogyan használható a Distcp segédprogramot.

1. Az asztalon SSH használatával csatlakozhat a fürthöz. Lásd: [csatlakozás Linux-alapú HDInsight-fürthöz](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Futtassa a parancsokat a SSH használatával.

2. Győződjön meg arról, hogy hozzáférhet-e az Azure Storage Blobs (WASB). Futtassa az alábbi parancsot:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    A kimenet kell biztosítania az storage blob tartalmának listáját.

3. Ehhez hasonlóan ellenőrizze, hogy a Data Lake Storage Gen1 fiók hozzáférhet a fürtből. Futtassa az alábbi parancsot:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    A kimenet a Data Lake Storage Gen1 fiókban lévő fájlok és mappák listáját kell biztosítania.

4. A Distcp használatával adatokat másol a WASB egy Data Lake Storage Gen1 fiókhoz.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    A parancs másolja a tartalmát a **/példa/data/gutenberg/** a WASB-mappában **/myfolder** a Data Lake Storage Gen1 fiókban.

5. Ehhez hasonlóan a Distcp használatával adatokat másol a Data Lake Storage Gen1 fiók a WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    A parancs másolja a tartalmát **/myfolder** , a Data Lake Storage Gen1 fiókban **/példa/data/gutenberg/** WASB-mappában.

## <a name="performance-considerations-while-using-distcp"></a>A DistCp használata közben a teljesítménnyel kapcsolatos szempontok

Mivel a DistCp a legalacsonyabb granularitási egyetlen fájlt, beállítás egyidejű példányok maximális száma az a legfontosabb paraméter, a Data Lake Storage Gen1 elleni optimalizálhatja azt. Egyidejű példányszám vezérli leképező számának beállítása ('M ') paraméter a parancssorban. Ez a paraméter leképező adatmásolás céljából használt maximális számát. Alapértelmezett értéke 20.

**Példa**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan állapítható meg a használandó leképező száma?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: Határozza meg a YARN teljes memória** – az első lépés az, hogy határozza meg a YARN a fürt, amelyen futtatja a DistCp feladat számára elérhető memória. Ez az információ a fürthöz társított Ambari portálon érhető el. Keresse meg a YARN és a Configs lapon, a YARN memória megtekintéséhez. A teljes YARN memória lekéréséhez szorozza meg a YARN-csomópontonkénti memória a csomópontok számát a fürt rendelkezik.

* **2. lépés: Leképező számának kiszámítása** -értékét **m** egyenlő teljes YARN memória, a YARN-tároló mérete elosztva a hányadosa. A YARN tároló mérete információkat, valamint az Ambari-portálon érhető el. Keresse meg a YARN és a konfigurációkat lapon. A YARN tárolóméret ebben az ablakban jelenik meg. Ahhoz, hogy eljusson leképező száma a egyenlőségi (**m**) van

        m = (number of nodes * YARN memory for each node) / YARN container size

**Példa**

Tegyük fel, hogy a fürt egy 4 D14v2s csomóponttal rendelkezik, és 10 TB-nyi adat átvitele 10 különböző mappákat szeretne. A mappák mindegyike a különböző mennyiségű adatot tartalmaz, és a fájlméret belül különböznek.

* Teljes YARN memória - portálon az Ambari meghatározni, hogy a YARN memória 96 GB egy D14 csomópont. Így a YARN memória összesen négy csomópontot tartalmazó fürtben van: 

        YARN memory = 4 * 96GB = 384GB

* Ennyi leképező - meghatározni, hogy a YARN tárolóméret 3072 D14 fürtcsomópont esetén az Ambari-portálon. Tehát van leképező száma:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Ha más alkalmazásokat használ a memóriát, majd lehet váltani, csak a fürt YARN memória egy részét a DistCp használják.

### <a name="copying-large-datasets"></a>Nagy mennyiségű adat másolása

Ha az adatkészlet áthelyezésének mérete nagy (például > 1 TB-ot), vagy ha több különböző mappa, akkor érdemes megfontolni a DistCp több feladatot. Valószínűleg nem nincs teljesítmény miatt, de terjeszti ki a feladatokat, hogy minden olyan feladat sikertelen lesz, ha csak a teljes feladat ahelyett, hogy az adott feladat újraindításához.

### <a name="limitations"></a>Korlátozások

* A DistCp megpróbálja létrehozni a teljesítmény optimalizálása érdekében mérete hasonló leképező. Leképező számának növelésével előfordulhat, hogy nem mindig növelheti teljesítményt.

* A DistCp fájlonként egyetlen eseményleképező korlátozódik. Így nem kell további leképező, mint a fájlokat. A DistCp csak rendelhet egy eseményleképező egy fájlt, mivel ez használható, nagy méretű fájlok másolása egyidejűségi mennyiségét korlátozza.

* Ha kevés nagyméretű fájlokat, majd akkor fel kell őket adattömbökbe 256 MB-os fájlt a lehetséges több egyidejűséget biztosít. 
 
* Másol egy Azure Blob Storage-fiókból, ha a másolási feladat szabályozva előfordulhat, hogy a blob storage-oldalon. Ez csökkenti a másolási feladat teljesítményét. Az Azure Blob Storage a korlátozásokkal kapcsolatos további tudnivalókért tekintse meg az Azure Storage-korlátok, [Azure-előfizetés- és Szolgáltatáskorlátok](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Lásd még
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

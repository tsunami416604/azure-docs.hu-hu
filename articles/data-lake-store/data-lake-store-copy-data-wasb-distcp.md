---
title: Adatok másolása a WASB-ba és az Azure Data Lake Storage Gen1 szolgáltatásba a DistCp használatával
description: A DistCp eszközzel adatokat másolhat az Azure Storage-blobokba és az Azure Data Lake Storage Gen1-be
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638833"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage-blobok és az Azure Data Lake Storage Gen1 között a DistCp használatával

> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Ha rendelkezik egy HDInsight-fürttel, amely hozzáfér az Azure Data Lake Storage Gen1 szolgáltatáshoz, a Hadoop ökoszisztéma-eszközeivel, például a DistCp-vel adatokat másolhat egy HDInsight fürttárolóba (WASB) egy Data Lake Storage Gen1 fiókba. Ez a cikk a DistCp eszköz használatát mutatja be.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1 fiók.** Az Azure Data Lake Storage Gen1 – [Első lépések az Azure Data Lake Storage Gen1 című](data-lake-store-get-started-portal.md)témakörben található.
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen1 fiókhoz. Lásd: [HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással című témakört.](data-lake-store-hdinsight-hadoop-use-portal.md) Győződjön meg arról, hogy engedélyezi a Távoli asztal szolgáltatást a fürtszámára.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>A DistCp használata HDInsight Linux-fürtről

A HDInsight-fürt a DistCp-eszközt használja, amellyel különböző forrásokból származó adatokat másolhat egy HDInsight-fürtbe. Ha úgy állította be a HDInsight-fürtöt, hogy a Data Lake Storage Gen1-et használja további tárolóként, a DistCp beépített használatával adatokat másolhat egy Data Lake Storage Gen1 fiókba és onnan. Ebben a szakaszban a DistCp eszköz használatát vizsgáljuk.

1. Az asztalról az SSH segítségével csatlakozzon a fürthöz. Lásd: [Csatlakozás Linux alapú HDInsight-fürthöz](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Futtassa a parancsokat az SSH parancssorból.

1. Ellenőrizze, hogy hozzáférhet-e az Azure Storage-blobok (WASB). Futtassa az alábbi parancsot:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   A kimenet a storage blob tartalomlistáját tartalmazza.

1. Hasonlóképpen ellenőrizze, hogy a fürtből hozzá tud-e férni a Data Lake Storage Gen1 fiókhoz. Futtassa az alábbi parancsot:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    A kimenet a Data Lake Storage Gen1 fiókban lévő fájlok és mappák listáját tartalmazza.

1. A DistCp használatával adatokat másolhat a WASB-ból egy Data Lake Storage Gen1 fiókba.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    A parancs a WASB **/example/data/gutenberg/** mappájának tartalmát a Data Lake Storage Gen1 fiók **/myfolder** mappájába másolja.

1. Hasonlóképpen a DistCp használatával adatokat másolhat egy Data Lake Storage Gen1 fiókból a WASB-ba.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    A parancs a **/myfolder** tartalmát a Data Lake Storage Gen1 fiókban a **WASB /example/data/gutenberg/** mappájába másolja.

## <a name="performance-considerations-while-using-distcp"></a>Teljesítménybeli szempontok a DistCp használata közben

Mivel a DistCp eszköz legalacsonyabb részletessége egyetlen fájl, az egyidejű másolatok maximális számának beállítása a legfontosabb paraméter a Data Lake Storage Gen1-hez való optimalizáláshoz. Az egyidejű másolatok számát a parancssorban a leképező ("m') paraméter számának beállításával szabályozhatja. Ez a paraméter az adatok másolásához használt leképezők maximális számát adja meg. Az alapértelmezett érték 20.

Példa:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Hogyan állapítható meg a használható leképezők száma?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: A yarn memória teljes meghatározása** – Az első lépés a DistCp-feladat futtatásához szükséges fürt számára elérhető YARN-memória meghatározása. Ez az információ a fürthöz társított Ambari portálon érhető el. Keresse meg a YARN lapot, és tekintse meg a **Configs** fület a YARN memória megtekintéséhez. A teljes YARN-memória leolvasásához szorozza meg a YARN-memóriát csomópontonként a fürtben lévő csomópontok számával.

* **2. lépés: Számítsuk ki a leképezők számát** - Az **m** érték egyenlő a yarn memória és a YARN tároló méretének hányadosával. A YARN tároló méretére vonatkozó információk az Ambari portálon is elérhetők. Nyissa meg a YARN lapot, és tekintse meg a **Configs** lapot. Ebben az ablakban jelenik meg a YARN tároló mérete. A leképezők (**m**) számának megérkezése a következő:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Példa:

Tegyük fel, hogy négy D14v2s csomópont van a fürtben, és 10 TB adatot szeretne átvinni 10 különböző mappából. Mindegyik mappa különböző mennyiségű adatot tartalmaz, és az egyes mappákon belüli fájlméretek eltérőek.

* YarN-memória összesen – Az Ambari portálról megállapítja, hogy a YARN memória 96 GB egy D14-csomópont esetében. Így a teljes YARN memória négy csomópontfürthöz:

   `YARN memory = 4 * 96GB = 384GB`

* Leképezők száma – Az Ambari portálról megállapíthatja, hogy a YARN-tároló mérete 3072 egy D14 fürtcsomópont esetében. Tehát a leképezők száma:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Ha más alkalmazások is használnak memóriát, választhatja azt is, hogy a fürt YARN-memóriájának csak egy részét használja a DistCp szolgáltatáshoz.

### <a name="copying-large-datasets"></a>Nagy adatkészletek másolása

Ha az áthelyezendő adatkészlet mérete nagy (például 1 TB >), vagy ha sok különböző mappával rendelkezik, fontolja meg több DistCp-feladat használatát. Valószínűleg nincs teljesítménynyereség, de elosztja a feladatokat, így ha bármelyik feladat sikertelen, csak az adott feladatot kell újraindítani a teljes feladat helyett.

### <a name="limitations"></a>Korlátozások

* A DistCp a teljesítmény optimalizálása érdekében hasonló méretű leképezőket próbál létrehozni. A leképezők számának növelése nem mindig növeli a teljesítményt.

* A DistCp fájlonként csak egy leképezőre korlátozódik. Ezért nem lehet több leképező, mint amennyi fájlja van. Mivel a DistCp csak egy leképezőt rendelhet egy fájlhoz, ez korlátozza a nagy fájlok másolásához használható egyidejűség mértékét.

* Ha kis számú nagy fájllal rendelkezik, ossza fel őket 256 MB-os fájldarabokra, hogy több potenciális egyidejűséget biztosítson.

* Ha egy Azure Blob-tárfiókból másol, előfordulhat, hogy a másolási feladat a Blob storage oldalán lesz szabályozva. Ez rontja a másolási feladat teljesítményét. Ha többet szeretne megtudni az Azure Blob storage korlátairól, olvassa el az Azure Storage-korlátozások at [Azure-előfizetési és szolgáltatáskorlátok című témakört.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="see-also"></a>Lásd még

* [Adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1 szolgáltatásba](data-lake-store-copy-data-azure-storage-blob.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)

---
title: Használja az Azure Data Box adatainak áttelepítéséhez a helyszíni HDFS tárolhatja az Azure Storage
description: A helyszíni HDFS adattárba adatok áttelepítése az Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/05/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9a42135df38cde91cc6626a3f7d0328334af0a5d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729039"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Azure Data Box használatával a helyszíni HDFS adattárba adatok áttelepítése az Azure Storage

Áttelepítheti az adatokat a Hadoop-fürt (a blob storage vagy a Data Lake Storage Gen2) Azure Storage-bA a helyszíni HDFS adattárba egy Data Box-eszköz használatával. Egy 80 TB-os Data Box vagy a 770 TB-os Data Box (nagy erőforrásigényű) közül választhat.

Ez a cikk a feladatok elvégzéséhez nyújt segítséget:

:heavy_check_mark: Másolja az adatokat a Data Box vagy a Data Box nehéz eszköz.

:heavy_check_mark: A Microsoftnak az eszköz szállításra.

:heavy_check_mark: Helyezze át az adatokat a Data Lake Storage Gen2 tárfiók.

## <a name="prerequisites"></a>Előfeltételek

Ezeknek a dolgoknak az áttelepítés elvégzéséhez szüksége lesz.

* Egy Azure Storage-fiók, amely **nem** hierarchikus névtér engedélyezve van rajta.

* Ha az Azure Data Lake Storage Gen2-fiókhoz használni kívánt (egy tárfiókot, amely **does** engedélyezve van rajta hierarchikus névtér), majd hozzon létre ezen a ponton érdemes.

* Egy helyi Hadoop-fürtöt, amely tartalmazza a forrásadatokat.

* Egy [Azure Data Box-eszköz](https://azure.microsoft.com/services/storage/databox/).

    - [A Data Box ORDER](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) vagy [Data Box-(nagy erőforrásigényű)](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Miközben az eszköz rendezése, ne felejtse el válasszon egy tárfiókot, amely **nem** hierarchikus névtér engedélyezve van rajta. Ennek az az oka a Data Box-eszköz még nem támogatják a közvetlen feltöltése az Azure Data Lake Storage Gen2. Másolja be egy storage-fiókot, és hajtsa végre a másodpéldány az ADLS Gen2-fiókba kell. Ehhez útmutatást az alábbi lépések szerepelnek.
    - Kábelek, és csatlakozzon a [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) vagy [Data Box nehéz](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a helyszíni hálózathoz.

Ha készen áll, kezdjük.

## <a name="copy-your-data-to-a-data-box-device"></a>Másolja át az adatokat a Data Box-eszköz

Az adatok másolása a helyszíni HDFS áruházból származó, egy Data Box-eszköz meg fogja beállítása néhány dolgot, majd használja a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) eszközt.

Ha a másolt adatok mennyisége, több mint egy egyetlen Data Box kapacitását, vagy a Data Box nehéz egyetlen csomópont, amely tördelésével be, amelyek elférnek a eszközök méretek az adatkészlethez.

Kövesse az alábbi lépéseket a REST API -k a Blob/objektumtár keresztül adatok másolása a Data Box-eszközre. A REST API-felület fogja elérhetővé tenni az eszköz és a fürt egy HDFS tárolóként jelennek meg. 


1. Előtt másolja az adatokat a REST-en keresztül, azonosíthatja a biztonsági és kapcsolati primitívek a REST-felület, a Data Box vagy a Data Box nehéz csatlakozni. Jelentkezzen be a helyi webes felhasználói felület a Data Box, és nyissa meg **csatlakozás és másolás** lapot. Ellen az Azure storage-fiók az eszköz a **hozzáférési beállítások**, keresse meg és jelölje ki **REST**.

    !["Csatlakozás és másolás" lap](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Hozzáférés a tárfiókban és az adatok feltöltése párbeszédpanel, másolja a **Blob-szolgáltatásvégpont** és a **tárfiókkulcs**. A blob-szolgáltatásvégpont nincs megadva a `https://` és a záró perjellel.

    Ebben az esetben a végpont nem: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Az URI-t fogja használni a gazdagép része: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Egy vonatkozó példáért lásd: hogyan [REST http protokollon keresztüli csatlakozás](/azure/databox/data-box-deploy-copy-data-via-rest). 

     !["A tárfiók eléréséhez és adatok feltöltése" párbeszédpanel](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. A végpont és a Data Box vagy a Data Box nehéz csomópont IP-cím hozzáadása `/etc/hosts` minden egyes csomóponton.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Ha a DNS-néhány egyéb mechanizmust használ, győződjön meg, amely a Data Box végpont feloldható.
    
4. Állítsa be egy felületváltozóban `azjars` átirányítása a `hadoop-azure` és a `microsoft-windowsazure-storage-sdk` jar-fájlok. Ezeket a fájlokat a Hadoop a telepítési könyvtárban van (ha ezek a fájlok vannak a következő paranccsal ellenőrizheti `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` ahol `<hadoop_install_dir>` a könyvtár, ha telepítette a Hadoop) használata a teljes elérési útját. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. Hozzon létre az adatok másolása használni kívánt tárolót. Ez a parancs részeként kell adnia a célmappába. Ez egy helyőrző célmappát ezen a ponton lehet.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. Győződjön meg arról, hogy a tároló és a mappában létrehozott lista-parancs futtatása.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. Adatok másolása a Hadoop a HDFS-ből a Data Box Blob storage, a tárolóba, amelyet korábban hozott létre. Nem található a mappát, másolja be, ha a parancs automatikusan létrehozza.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   A `-libjars` kapcsolóval, hogy a `hadoop-azure*.jar` és a függő `azure-storage*.jar` elérhető fájlok `distcp`. Ez már előfordulhat egyes fürtökben.
   
   A következő példa bemutatja a `distcp` parancs adatok másolása szolgál.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
A másolási sebesség javításához:
- Próbálja meg módosítani a leképező számát. (A fenti példában `m` = 4 leképező.)
- Próbálja meg futtatni több `distcp` párhuzamosan.
- Ne feledje, hogy a nagy méretű fájlok jobban, mint a kisméretű fájlok hajtható végre.
    
## <a name="ship-the-data-box-to-microsoft"></a>A Data Box, a Microsoft szállításra

Kövesse az alábbi lépéseket, előkészítése és a Microsoft Data Box-eszköze szállításra.

1. Az adatmásolás befejezése után futtassa:
    
    - [A Data Box vagy a Data Box nehéz szállításra való](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).
    - Miután az eszköz előkészítése befejeződött, töltse le a AJ fájlokat. Ezek AJ használja, vagy később, ellenőrizze az adatokat az Azure-bA feltöltött fájlok manifest lesz. 
    - Állítsa le az eszközt, és távolítsa el a kábelek.
2.  Ütemezhet begyűjtést a UPS. Kövesse az utasításokat:

    - [A Data Box szállításra](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 
    - [A Data Box (nagy erőforrásigényű) szállításra](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).
3.  Microsoft megkapja az eszköz, az adatközponti hálózathoz csatlakozik, és az adatok a storage-fiók (a hierarchikus névterek le van tiltva) a megadott fel a rendszer mikor meg helyezi az eszközt ahhoz. Ellenőrizze a AJ fájlokra vonatkozóan, hogy az Azure-bA feltöltött összes adatát. Most már továbbléphet az adatok Data Lake Storage Gen2-tárfiókra.


## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Helyezze át az adatokat a Data Lake Storage Gen2 tárfiók alakzatot

Ez a lépés akkor szükséges, ha használata Azure Data Lake Storage Gen2 lesz az adattároló. Ha csak egy blob storage-fiók nélkül hierarchikus névtér lesz az adattároló használ, nem kell erre a lépésre szükség.

Ezt kétféleképpen teheti meg.

- Használat [Azure Data Factoryben az adatok áthelyezése az ADLS Gen2-re](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Adja meg kell **Azure Blob Storage** forrásaként.

- Az Azure-alapú Hadoop-fürt használja. A DistCp parancsot futtathatja:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Ez a parancs másol adatokat és a metaadatokat a tárfiók a Data Lake Storage Gen2 tárfiókba.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy a Data Lake Storage Gen2 hogyan használható a HDInsight-fürtökkel. Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).

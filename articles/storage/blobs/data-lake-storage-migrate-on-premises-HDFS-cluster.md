---
title: Használja az Azure Data Box adatainak áttelepítéséhez a helyszíni HDFS tárolhatja az Azure Storage
description: A helyszíni HDFS adattárba adatok áttelepítése az Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0a658d47f850537f18c976ab0933105b57692673
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344759"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Azure Data Box használatával a helyszíni HDFS adattárba adatok áttelepítése az Azure Storage

Áttelepítheti az adatokat a Hadoop-fürt (a blob storage vagy a Data Lake Storage Gen2) Azure Storage-bA a helyszíni HDFS adattárba egy Data Box-eszköz használatával.

Ez a cikk a feladatok elvégzéséhez nyújt segítséget:

:heavy_check_mark: Másolja az adatokat a Data Box-eszközre.

:heavy_check_mark: A Microsoft Data Box-eszköze szállításra.

:heavy_check_mark: Helyezze át az adatokat a Data Lake Storage Gen2 tárfiók.

## <a name="prerequisites"></a>Előfeltételek

Ezeknek a dolgoknak az áttelepítés elvégzéséhez szüksége lesz.

* Egy Azure Storage-fiók, amely **nem** hierarchikus névtér engedélyezve van rajta.

* Ha az Azure Data Lake Storage Gen2-fiókhoz használni kívánt (egy tárfiókot, amely **does** engedélyezve van rajta hierarchikus névtér), majd hozzon létre ezen a ponton érdemes.

* Egy helyi Hadoop-fürtöt, amely tartalmazza a forrásadatokat.

* Egy [Azure Data Box-eszköz](https://azure.microsoft.com/services/storage/databox/). 

    - [A Data Box ORDER](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered). Rendezés a Box, miközben a vágólapra válasszon egy tárfiókot, amely **nem** hierarchikus névtér engedélyezve van rajta. Ennek az az oka a Data Box még nem támogatja a közvetlen feltöltése az Azure Data Lake Storage Gen2. Másolja be egy storage-fiókot, és hajtsa végre a másodpéldány az ADLS Gen2-fiókba kell. Ehhez útmutatást az alábbi lépések szerepelnek.
    - [Bekötéséhez és csatlakozás a Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) a helyszíni hálózathoz.

Ha készen áll, kezdjük.

## <a name="copy-your-data-to-a-data-box-device"></a>Másolja át az adatokat a Data Box-eszköz

Az adatok másolása a helyszíni HDFS áruházból származó, egy Data Box-eszköz meg fogja beállítása néhány dolgot, majd használja a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) eszközt.

Ha a másolt adatok mennyisége több mint egy egyetlen Data Box kapacitását, akkor az adatkészlethez, az adatok mezőkbe igazodó méretű részekre.

Kövesse az alábbi lépéseket az adatok a REST API -k a Blob/objektumtár keresztül másolásához a Data Box. A REST API-felület fogja elérhetővé tenni a Data Box, a fürt HDFS tárolóként jelennek meg. 


1. Másolja az adatokat a REST-en keresztül, mielőtt azonosíthatja a biztonsági és kapcsolati primitívek szeretne csatlakozni a REST-felület, a Data Box. Jelentkezzen be a helyi webes felhasználói felület a Data Box, és nyissa meg **csatlakozás és másolás** lapot. Ellen az Azure storage-fiók a Data Box alatt **hozzáférési beállítások**, keresse meg és jelölje ki **REST(Preview)**.

    !["Csatlakozás és másolás" lap](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Hozzáférés a tárfiókban és az adatok feltöltése párbeszédpanel, másolja a **Blob-szolgáltatásvégpont** és a **tárfiókkulcs**. A blob-szolgáltatásvégpont nincs megadva a `https://` és a záró perjellel.

    Ebben az esetben a végpont nem: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Az URI-t fogja használni a gazdagép része: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Egy vonatkozó példáért lásd: hogyan [REST http protokollon keresztüli csatlakozás](/azure/databox/data-box-deploy-copy-data-via-rest.md). 

     !["A tárfiók eléréséhez és adatok feltöltése" párbeszédpanel](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. A végpont és a Data Box IP-cím hozzáadása `/etc/hosts` minden egyes csomóponton.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Ha a DNS-néhány egyéb mechanizmust használ, győződjön meg, amely a Data Box végpont feloldható.
    
3. Állítsa be egy felületváltozóban `azjars` átirányítása a `hadoop-azure` és a `microsoft-windowsazure-storage-sdk` jar-fájlok. Ezeket a fájlokat a Hadoop a telepítési könyvtárban van (ha ezek a fájlok vannak a következő paranccsal ellenőrizheti `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` ahol `<hadoop_install_dir>` a könyvtár, ha telepítette a Hadoop) használata a teljes elérési útját. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

4. Adatok másolása a Hadoop a HDFS-ből a Data Box blobtárolóba.

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
- Próbálja meg futtatni a több `distcp` párhuzamosan.
- Ne feledje, hogy a nagy méretű fájlok jobban, mint a kisméretű fájlok hajtható végre.       
    
## <a name="ship-the-data-box-to-microsoft"></a>A Data Box, a Microsoft szállításra

Kövesse az alábbi lépéseket, előkészítése és a Microsoft Data Box-eszköze szállításra.

1. Az adatmásolás befejezése után futtassa [szállításra való](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#prepare-to-ship) a Data Box. Miután az eszköz előkészítése befejeződött, töltse le a AJ fájlokat. Ezek AJ használja, vagy később, ellenőrizze az adatokat az Azure-bA feltöltött fájlok manifest lesz. Állítsa le az eszközt, és távolítsa el a kábelek. 
2.  Ütemezhet begyűjtést a UPS- [vissza az Azure-bA a Data Box szállításra](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up). 
3.  Microsoft megkapja az eszköz, csatlakoztatva van a hálózati adatközpontba, és adatokat töltenek fel a tárfiók a megadott (a hierarchikus névterek le van tiltva) után mikor megrendelt a Data Box. Ellenőrizze a AJ fájlokra vonatkozóan, hogy az Azure-bA feltöltött összes adatát. Most már továbbléphet az adatok Data Lake Storage Gen2-tárfiókra.

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Helyezze át az adatokat a Data Lake Storage Gen2 tárfiók alakzatot

Ez a lépés akkor szükséges, ha használata Azure Data Lake Storage Gen2 lesz az adattároló. Ha csak egy blob storage-fiók nélkül hierarchikus névtér lesz az adattároló használ, nem kell erre a lépésre szükség.

Ez 2 módon teheti meg. 

- Használat [Azure Data Factoryben az adatok áthelyezése az ADLS Gen2-re](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Adja meg kell **Azure Blob Storage** forrásaként.

- Az Azure-alapú Hadoop-fürt használja. A DistCp parancsot futtathatja:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Ez a parancs másol adatokat és a metaadatokat a tárfiók a Data Lake Storage Gen2 tárfiókba.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy a Data Lake Storage Gen2 hogyan használható a HDInsight-fürtökkel. Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](https://docs.microsoft.com/Azure/storage/blobs/data-lake-storage-use-hdi-cluster?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

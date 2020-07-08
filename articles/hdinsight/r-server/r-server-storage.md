---
title: Azure Storage-megoldások a HDInsight-beli ML-szolgáltatásokhoz – Azure
description: További információ a HDInsight ML-szolgáltatásaival elérhető különböző tárolási lehetőségekről
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75660239"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure Storage-megoldások az Azure HDInsight ML-szolgáltatásaihoz

A HDInsight ML-szolgáltatásai különböző tárolási megoldásokat használhatnak az elemzésből származó eredményeket tartalmazó adatok, kódok vagy objektumok megőrzésére. Ezek a megoldások a következő lehetőségeket tartalmazzák:

- [Azure-blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure file Storage](https://azure.microsoft.com/services/storage/files/)

Lehetősége van több Azure Storage-fiók vagy-tároló elérésére is a HDInsight-fürttel. Az Azure file Storage egy kényelmes adattárolási lehetőség a peremhálózati csomóponton, amely lehetővé teszi egy Azure Storage-fájlmegosztás csatlakoztatását, például a Linux fájlrendszert. Az Azure-fájlmegosztás azonban bármely olyan rendszer számára csatlakoztatható és használható, amely támogatott operációs rendszerrel rendelkezik, például Windows vagy Linux.

Ha Apache Hadoop-fürtöt hoz létre a HDInsight-ben, akkor egy **Azure Storage** -fiókot vagy **Data Lake Storage**kell megadnia. A fiókból egy adott tároló tárolja a létrehozott fürt fájlrendszerét (például a Hadoop elosztott fájlrendszer). További információt és útmutatást a következő témakörben talál:

- [Az Azure Storage és a HDInsight használata](../hdinsight-hadoop-use-blob-storage.md)
- [Data Lake Storage használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Azure Blob Storage-fiókok használata ML Services-fürttel

Ha több Storage-fiókot adott meg a ML-szolgáltatások fürtjének létrehozásakor, az alábbi utasítások azt ismertetik, hogyan használhatók másodlagos fiókok az adathozzáféréshez és a műveletekhez egy ML-szolgáltatások fürtjén. Tegyük fel, hogy a következő Storage-fiókok és-tárolók: **storage1** és a **container1**nevű alapértelmezett tároló, valamint a **storage2** és a **container2**.

> [!WARNING]  
> A teljesítmény érdekében a HDInsight-fürt ugyanabban az adatközpontban jön létre, mint a megadott elsődleges Storage-fiók. A HDInsight-fürttől eltérő helyen lévő Storage-fiók használata nem támogatott.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Az alapértelmezett tároló használata a HDInsight ML-szolgáltatásaival

1. Egy SSH-ügyfél használatával csatlakozzon a fürt peremhálózati csomópontjához. Az SSH HDInsight-fürtökkel való használatával kapcsolatban lásd: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Másolja a mysamplefile.csv a/Share könyvtárba.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Váltson az R Studióra vagy egy másik R-konzolra, és írja be az R-kódot, hogy a név csomópontot az **alapértelmezett** értékre állítsa, és az elérni kívánt fájl helyét.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

A könyvtár és a fájl összes hivatkozása a Storage-fiókra mutat `wasbs://container1@storage1.blob.core.windows.net` . Ez az **alapértelmezett Storage-fiók** , amely a HDInsight-fürthöz van társítva.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>A kiegészítő tároló használata a HDInsight ML-szolgáltatásaival

Most tegyük fel, hogy egy mysamplefile1.csv nevű fájlt szeretne feldolgozni a **storage2**-ben található **container2** /Private könyvtárban.

Az R-kódban mutasson a név csomópontra hivatkozás a **storage2** Storage-fiókra.

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Az összes címtár-és fájl-hivatkozás most a Storage-fiókra mutat `wasbs://container2@storage2.blob.core.windows.net` . Ez a **név megadott csomópontja** .

Konfigurálja a `/user/RevoShare/<SSH username>` könyvtárat a **storage2** a következő módon:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Azure Data Lake Storage használata a ML-szolgáltatások fürtjével

Ha Data Lake Storaget szeretne használni a HDInsight-fürttel, a fürtnek hozzáférést kell adnia minden használni kívánt Azure Data Lake Storagehoz. A HDInsight-Azure Data Lake Storage fürtök alapértelmezett tárolóként vagy további tárolóként való létrehozásával kapcsolatos Azure Portal útmutatásért lásd: HDInsight-fürt létrehozása a [Data Lake Storage használatával Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Ezután a tárolót az R-szkriptben ugyanúgy használhatja, mint egy másodlagos Azure Storage-fiókot, az előző eljárásban leírtak szerint.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Fürthöz való hozzáférés hozzáadása a Azure Data Lake Storagehoz

Data Lake Storage a HDInsight-fürthöz társított Azure Active Directory (Azure AD) egyszerű szolgáltatásnév használatával férhet hozzá.

1. A HDInsight-fürt létrehozásakor válassza ki a **fürt HRE-identitás** elemet az **adatforrás** lapról.

2. A **fürt HRE-identitása** párbeszédpanel **Active Directory-szolgáltatás kiválasztása**területén válassza az **új létrehozása**lehetőséget.

Miután megadta a szolgáltatásnév nevét, és létrehoz egy jelszót, kattintson a ADLS- **hozzáférés kezelése** elemre az egyszerű szolgáltatásnév a Data Lake Storage való hozzárendeléséhez.

A fürt létrehozását követően egy vagy több Data Lake Storage-fiókhoz is hozzá lehet adni a fürthöz való hozzáférést. Nyissa meg a Data Lake Storage Azure Portal bejegyzését, és lépjen a **Adatkezelő > Access > Hozzáadás**gombra.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Data Lake Storage Gen1 elérése a HDInsight ML-szolgáltatásaiból

Miután megadta a hozzáférést a Data Lake Storage Gen1hoz, használhatja a HDInsight ML Services-fürtön a tárolót a másodlagos Azure Storage-fiókkal. Az egyetlen különbség, hogy az előtag **wasbs://** a következőképpen módosítja a **ADL://** :

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

A következő parancsok segítségével konfigurálhatja a Data Lake Storage Gen1 fiókot a RevoShare könyvtárral, és hozzáadhatja a minta. csv fájlt az előző példából:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Az Azure file Storage használata ML-szolgáltatásokkal a HDInsight-on

Egy kényelmes adattárolási lehetőség is használható a [Azure Files](https://azure.microsoft.com/services/storage/files/)nevű peremhálózati csomóponton. Lehetővé teszi az Azure Storage-fájlmegosztás csatlakoztatását a Linux fájlrendszerhez. Ez a beállítás hasznos lehet az adatfájlok, az R-parancsfájlok és az olyan eredmények tárolására, amelyekre később szükség lehet, különösen akkor, ha a natív fájlrendszert a HDFS helyett a peremhálózati csomóponton szeretné használni.

A Azure Files egyik fő előnye, hogy a fájlmegosztás csatlakoztatható és felhasználható bármely olyan rendszer számára, amely támogatott operációs rendszerrel (például Windows vagy Linux) rendelkezik. Használhatja például egy másik HDInsight-fürt, amelyet Ön vagy valaki a csapatában, egy Azure-beli virtuális gép vagy akár egy helyszíni rendszer használatával. További információkért lásd:

- [Az Azure file Storage használata Linux rendszeren](../../storage/files/storage-how-to-use-files-linux.md)
- [Az Azure file Storage használata Windows rendszeren](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>További lépések

- [A ML Services-fürt áttekintése a HDInsight-on](r-server-overview.md)
- [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

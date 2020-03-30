---
title: Azure tárolási megoldások ml-szolgáltatásokhoz a HDInsighton – Azure
description: Ismerje meg az ML-szolgáltatások hdinsight-alapú modullal elérhető különböző tárolási lehetőségeket
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660239"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure tárolási megoldások ml-szolgáltatásokhoz az Azure HDInsightban

A HDInsight ML-szolgáltatásai különböző tárolási megoldásokat használhatnak az elemzésből származó eredményeket tartalmazó adatok, kódok vagy objektumok megőrzéséhez. Ezek a megoldások a következő lehetőségeket tartalmazzák:

- [Azure-blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure-fájltárolás](https://azure.microsoft.com/services/storage/files/)

Azt is lehetősége van több Azure storage-fiókok vagy tárolók hdinsight-fürttel való eléréséhez. Az Azure File storage egy kényelmes adattárolási lehetőség a peremhálózati csomóponton, amely lehetővé teszi, hogy egy Azure storage-fájlmegosztást, például a Linux fájlrendszercsatlakoztatása. De az Azure File-megosztások csatlakoztathatók és használhatók bármely olyan rendszer által, amely támogatott operációs rendszerrel rendelkezik, például Windows vagy Linux.

Amikor létrehoz egy Apache Hadoop-fürtöt a HDInsightban, meg kell adnia egy **Azure Storage-fiókot** vagy **a Data Lake Storage-ot.** Az adott fiókból származó tároló tartalmazza a létrehozott fürt (például a Hadoop elosztott fájlrendszer) fájlrendszerét. További információ és útmutatás:

- [Az Azure Storage használata a HDInsight segítségével](../hdinsight-hadoop-use-blob-storage.md)
- [A Data Lake Storage használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Azure Blob storage-fiókok használata az ML Services-fürttel

Ha a ml-szolgáltatások fürtjének létrehozásakor egynél több tárfiókot adott meg, az alábbi utasítások ismertetik, hogyan használhat másodlagos fiókot az adathozzáféréshez és a ml services-fürthöz való műveletekhez. Tegyük fel, hogy a következő tárfiókok és tárolók: **storage1** és egy alapértelmezett tároló nevű **container1**, és **storage2** **container2**.

> [!WARNING]  
> A teljesítmény szempontjából a HDInsight-fürt ugyanabban az adatközpontban jön létre, mint a megadott elsődleges tárfiók. A HDInsight-fürttől eltérő helyen lévő tárfiók használata nem támogatott.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Az alapértelmezett tárhely használata az ML-szolgáltatásokkal a HDInsight-on

1. Egy SSH-ügyfél használatával csatlakozzon a fürt peremhálózati csomópontjához. Az SSH HDInsight-fürtökkel való használatáról az [SSH használata a HDInsight szolgáltatással](../hdinsight-hadoop-linux-use-ssh-unix.md)című témakörben talál további információt.
  
2. Másolja a mysamplefile.csv mintafájlt a /share könyvtárba.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Váltson az R Studio vagy egy másik R-konzolra, és írja be az R-kódot, hogy a névcsomópont **ot alapértelmezettre** és az elérni kívánt fájl helyére állítsa.  

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

Az összes könyvtár- és fájlhivatkozás `wasbs://container1@storage1.blob.core.windows.net`a tárfiókra mutat. Ez a HDInsight-fürthöz társított **alapértelmezett tárfiók.**

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>A további tárhely használata az ML-szolgáltatásokkal a HDInsight-on

Tegyük fel, hogy fel szeretne dolgozni egy mysamplefile1.csv nevű fájlt, amely a **storage2** 2 . **storage2**

Az R-kódban mutasson a name node hivatkozása a **storage2** tárfiókra.

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

Az összes könyvtár- és fájlhivatkozás a `wasbs://container2@storage2.blob.core.windows.net`tárfiókra mutat. Ez a megadott **névcsomópont.**

Konfigurálja `/user/RevoShare/<SSH username>` a könyvtárat a **storage2-n** az alábbiak szerint:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Az Azure Data Lake Storage használata ml-szolgáltatások fürtjével

A Data Lake Storage hdinsight-fürttel való használatához meg kell adnia a fürtnek a használni kívánt Azure Data Lake Storage-hoz való hozzáférést. Ha tudni szeretné, hogyan hozhat létre az Azure Portalon egy HDInsight-fürtöt alapértelmezett tárolóként vagy további tárolóként egy Azure Data Lake Storage-fiókkal, olvassa [el a HDInsight-fürt létrehozása](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)a Data Lake Storage használatával az Azure Portal használatával című témakört.

Ezután használja a tárolót az R-parancsfájlban ugyanúgy, mint egy másodlagos Azure-tárfiókot az előző eljárásban leírtak szerint.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Fürthozzáférés hozzáadása az Azure Data Lake Storage-hoz

A Data Lake Storage-t egy Azure Active Directory (Azure AD) service principal használatával érheti el, amely a HDInsight-fürthöz van társítva.

1. A HDInsight-fürt létrehozásakor válassza a **Fürt AAD-identitását** az **Adatforrás** lapon.

2. A **Fürt AAD-identitás párbeszédpanel** **Egyszerű AD szolgáltatásnév kiválasztása**csoportjában válassza **az Új létrehozása lehetőséget.**

Miután nevet adott a szolgáltatásnévnek, és jelszót adott hozzá, kattintson az **ADLS-hozzáférés kezelése** gombra a szolgáltatásnév nek a Data Lake Storage-hoz való társításához.

Fürthozzáférést is hozzáadhat egy vagy több Data Lake-tárfiókhoz a fürt létrehozását követően. Nyissa meg az Azure Portal bejegyzést egy Data Lake Storage számára, és nyissa meg **az Adatkezelő t > Access > Add**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>A Data Lake Storage Gen1 elérése az ML-szolgáltatásokból a HDInsight-on

Miután hozzáférést adott a Data Lake Storage Gen1-hez, használhatja a hdinsight ML Services-fürtben lévő tárolót, mint egy másodlagos Azure-tárfiókot. Az egyetlen különbség az, hogy az előtag **wasbs://** **a következőképpen** változik adl://:

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

A következő parancsokkal konfigurálhatja a Data Lake Storage Gen1 fiókot a RevoShare könyvtárral, és hozzáadhatja az előző példából származó .csv mintafájlt:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Azure File storage használata az ML-szolgáltatásokkal a HDInsight-on

Van is egy kényelmes adattárolási lehetőség használható a peremhálózati csomópont nevű [Azure Files.](https://azure.microsoft.com/services/storage/files/) Lehetővé teszi, hogy egy Azure Storage-fájlmegosztást csatlakoztatjon a Linux fájlrendszerhez. Ez a beállítás hasznos lehet az adatfájlok, R-parancsfájlok és eredményobjektumok tárolására, amelyekre később szükség lehet, különösen akkor, ha a HDFS helyett a hálózati fájlrendszert érdemes használni a peremhálózati csomóponton.

Az Azure Files egyik fő előnye, hogy a fájlmegosztások csatlakoztathatók és használhatók bármely olyan rendszer által, amely támogatott operációs rendszerrel rendelkezik, például A Windows vagy a Linux. Például használhatja egy másik HDInsight-fürt, amely Ön vagy a csapat egy tagja, egy Azure virtuális gép, vagy akár egy helyszíni rendszer által. További információkért lásd:

- [Az Azure File storage használata Linux alatt](../../storage/files/storage-how-to-use-files-linux.md)
- [Az Azure File storage használata Windows rendszeren](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>További lépések

- [Az ML Services fürt áttekintése a HDInsighton](r-server-overview.md)
- [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

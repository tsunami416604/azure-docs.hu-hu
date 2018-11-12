---
title: A HDInsight - Azure Machine Learning-szolgáltatások Azure Storage-megoldások
description: A HDInsight a Machine Learning-szolgáltatások különböző tárolási lehetőségek ismertetése
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 466cb9ea116030d3f6ee6b41809011dae9acc00b
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011204"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure Storage-megoldások Machine Learning-szolgáltatások az Azure HDInsight

Machine Learning szolgáltatások a HDInsight a tárolási megoldások széles megőrizni az adatokat, kódok vagy objektumokat, amelyek az elemzési eredményeket. Ezek közé tartozik a következő beállításokat:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Az Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)
- [Az Azure File storage](https://azure.microsoft.com/services/storage/files/)

Lehetősége is van, több Azure storage-fiókok és -tárolót a HDInsight-fürt eléréséhez. Az Azure File storage az kényelmes adatok tárolási lehetőség használatra az élcsomóponton, amely lehetővé teszi, hogy egy, az Azure Storage-fájlmegosztás csatlakoztatása például a Linux-fájlrendszer. De Azure-fájlmegosztások csatlakoztatva van, és bármilyen, amely rendelkezik egy támogatott operációs rendszert, például a Windows vagy Linux rendszert használják. 

Amikor a HDInsight egy Hadoop-fürtöt hoz létre, vagy megadhatja egy **az Azure storage** fiókot vagy egy **Data Lake store**. Ebből a fiókból egy adott tároló tárolja a fájlrendszer, a fürt által létrehozott (például a Hadoop elosztott fájlrendszer). További információkért és útmutatóért lásd:

- [Az Azure storage használata a HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Használata Data Lake Store az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Az Azure Blob storage-fiókok használata a Machine Learning-szolgáltatások-fürttel

Ha egynél több tárfiókot a Machine Learning-szolgáltatások fürt létrehozásakor megadott, az alábbi utasítások azt ismertetik, hogyan adatelérési és a egy Machine Learning-szolgáltatások fürt műveletek másodlagos fiókot használhatják. Tegyük fel, a következő tárfiókok és a tároló: **storage1** és a egy alapértelmezett tárolót **container1**, és **storage2** a **container2**.

> [!WARNING]
> A teljesítmény a HDInsight-fürt létrejön az Ön által megadott elsődleges tárfióknak ugyanabban az adatközpontban. A HDInsight-fürt, mint egy másik helyen lévő tárfiókok használata nem támogatott.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Az alapértelmezett tároló használata a HDInsight-ML szolgáltatásokkal

1. A fürt az élcsomópont SSH-ügyfelet használ, csatlakozzon. Az SSH és a HDInsight-fürtök további információkért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Egy mintafájlt, mysamplefile.csv, másolja a /share könyvtárba. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Váltson át az R Studio vagy egy másik R-konzolról, és állítsa a nevet csomópont R-kód írása **alapértelmezett** és az elérni kívánt fájl helyét.  

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

A tárfiók mutasson a minden fájl és könyvtár hivatkozás `wasb://container1@storage1.blob.core.windows.net`. Ez a **alapértelmezett tárfiók** , amely a HDInsight-fürthöz van társítva.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>A további tárhely használata a HDInsight Machine Learning-szolgáltatások

Most tegyük fel, amely a /private található mysamplefile1.csv nevű fájlba feldolgozni kívánt könyvtárát **container2** a **storage2**.

Az R-kód, a pont a csomópont hivatkozás a **storage2** storage-fiókot.

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
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

Összes fájl és könyvtár hivatkozása mostantól a tárfiókba pont `wasb://container2@storage2.blob.core.windows.net`. Ez a **neve csomópont** megadott.

A/User/RevoShare/konfigurálására<SSH username> könyvtárába **storage2** módon:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-ml-services-cluster"></a>Az Azure Data Lake Store használata a Machine Learning-szolgáltatások-fürttel 

Data Lake Store használata a HDInsight-fürt, kell a fürt hozzáférést biztosít minden Azure Data Lake Store, amelyet használni szeretne. Az Azure portal használata egy HDInsight-fürt létrehozása az alapértelmezett tárolóként, vagy egy kiegészítő tárolóként az Azure Data Lake Store-fiókkal kapcsolatos utasításokért lásd: [egy HDInsight-fürt létrehozása a Data Lake Store az Azure portal használatával](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Ezt követően használhatja az áruház az R-szkript sokkal megismert egy másodlagos Azure-tárfiókot az előző eljárásban leírtak szerint.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Az Azure Data Lake store a fürt hozzáférés hozzáadása
A Data Lake store egy Azure Active Directory (Azure AD) egyszerű szolgáltatás használatával, amely a HDInsight-fürthöz társított érheti el.

1. A HDInsight-fürt létrehozásakor válassza **fürt AAD-identitásával** származó a **adatforrás** fülre.

2. Az a **fürt AAD-identitásával** párbeszédpanel **AD-szolgáltatásnév kiválasztása**válassza **új létrehozása**.

Miután hozzon létre egy jelszót, és nevezze el az egyszerű szolgáltatás kattintson **ADLS-hozzáférés kezelése** a szolgáltatásnév társítása a Data Lake Store.

Akkor is fürt hozzáférés hozzáadása a fürt létrehozása a következő egy vagy több Data Lake Store fiókot. Nyisson meg egy Data Lake Store az Azure portal bejegyzését, és nyissa meg **adatkezelő > hozzáférés > Hozzáadás**. 

### <a name="how-to-access-the-data-lake-store-from-ml-services-on-hdinsight"></a>A Machine Learning szolgáltatások a HDInsight a Data Lake store elérése

Után hozzáférést adott, egy Data Lake Store, használhatja a Machine Learning szolgáltatások a fürtben tárolóban a HDInsight egy másodlagos Azure-tárfiók ugyanúgy. Az egyetlen különbség, hogy az előtag **wasb: / /** vált **adl: / /** módon:


    # Point to the ADL store (e.g. ADLtest)
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

A Data Lake Store-fiók konfigurálása RevoShare címtárhoz, és adja hozzá a .csv-mintafájlt az előző példában a következő parancsok segítségével:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Azure File storage használata a HDInsight Machine Learning-szolgáltatások

Van egy kényelmes adatok tárolási lehetőség, használatra az élcsomóponti operacionalizáláshoz nevű [Azure Files](https://azure.microsoft.com/services/storage/files/). Lehetővé teszi, hogy egy Azure-tárolófájl-megosztás és a Linux-fájlrendszer csatlakoztatási. Lehet, hogy ezt a beállítást az adatfájlok, R-szkriptek és az esetleg szükséges később, különösen akkor, ha a natív fájlrendszer használatára a HDFS helyett az élcsomóponthoz logikus eredményobjektumok tárolására is praktikus. 

Az Azure Files egyik fő előnye az, hogy a fájlmegosztások csatlakoztatva van-e, és hogy minden, amely rendelkezik egy támogatott operációs rendszer, például a Windows vagy Linux rendszeren használja. Például akkor használható, amelynek Ön vagy valaki a csapata egy másik HDInsight-fürt, egy Azure virtuális Gépen, vagy akár egy helyszíni rendszer szerint. További információ eléréséhez lásd:

- [Az Azure File Storage használata Linuxszal](../../storage/files/storage-how-to-use-files-linux.md)
- [A Windows Azure File storage használata](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>További lépések

* [Machine Learning-szolgáltatások HDInsight-fürt áttekintése](r-server-overview.md)
* [Machine Learning-szolgáltatások fürt hadoop használatának első lépései](r-server-get-started.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)


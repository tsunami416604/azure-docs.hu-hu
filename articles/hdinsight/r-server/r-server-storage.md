---
title: Az R Server on HDInsight - Azure Azure tárolási megoldások |} Microsoft Docs
description: További információk a HDInsight R Server elérhető más tárolási lehetőségek
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: 23e32a913fb73d2207f7cf37ce6230e428fbe95c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-storage-solutions-for-r-server-on-azure-hdinsight"></a>Az R Server on Azure HDInsight az Azure tárolási megoldások

Az R Server on HDInsight számos különféle tárolási megoldásokkal megőrizni az adatokat, kódok vagy elemzés eredményeinek tartalmazó objektumokra. Ezek közé tartozik a következő beállításokat:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Az Azure Data Lake-tároló](https://azure.microsoft.com/services/data-lake-store/)
- [Az Azure File storage](https://azure.microsoft.com/services/storage/files/)

Lehetősége is van a több Azure storage-fiókok vagy tárolók a HDInsight-fürthöz való hozzáférés. Az Azure File storage az adatok tárolási lehetőség használható a peremhálózati csomóponton, amely lehetővé teszi, hogy csatlakoztassa egy Azure Storage-megosztás és, például a Linux fájlrendszer. Azonban az Azure fájlmegosztások csatlakoztatva, és a rendszer, például a Windows vagy Linux támogatott operációs rendszer által használt. 

Hdinsight Hadoop-fürt létrehozásakor, vagy megadhatja egy **az Azure storage** fiók vagy egy **Data Lake store**. Ebből a fiókból adott tárolót a fájlrendszer a fürt létrehozásakor (például a Hadoop elosztott fájlrendszerből) tartalmazza. További információt és útmutatást lásd:

- [Az Azure storage használata a hdinsight eszközzel](../hdinsight-hadoop-use-blob-storage.md)
- [Használjon Data Lake Store az Azure HDInsight-fürtök](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-r-server-cluster"></a>R Server-fürt Azure Blob storage-fiókok használata

Ha egynél több tárfiókot az R Server-fürt létrehozásakor megadott, az alábbi utasításokat ismerteti az adatok elérése és a műveletek R Server-fürt a másodlagos fiók használata. Tegyük fel, a következő tárfiókok és a tároló: **storage1** és egy alapértelmezett tároló nevű **container1**, és **storage2** rendelkező **container2**.

> [!WARNING]
> Teljesítmény érdekében a HDInsight-fürt létrehozása a megadott elsődleges tárfiókkal azonos adatközpontba. A storage-fiók egy másik helyen, mint a HDInsight-fürt használata nem támogatott.

### <a name="use-the-default-storage-with-r-server-on-hdinsight"></a>Az alapértelmezett tárhelyet használ az R Server on HDInsight

1. A fürt élcsomópont csatlakozni egy SSH-ügyfélprogram segítségével. Az SSH használata a HDInsight-fürtök információkért lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Egy fájl, mysamplefile.csv, másolja a /share könyvtárba. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Váltson át R Studio vagy egy másik R-konzolban, és a név csomópont állítható R-kód írása **alapértelmezett** és az elérni kívánt fájl helyét.  

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

A könyvtár- és hivatkozások mutasson a tárfiók `wasb://container1@storage1.blob.core.windows.net`. Ez a **alapértelmezett tárfiók** , amely a HDInsight-fürthöz van társítva.

### <a name="use-the-additional-storage-with-r-server-on-hdinsight"></a>A további tárhelyet használ az R Server on HDInsight

Most tegyük fel szeretne dolgozni egy fájlt, amely a /private található mysamplefile1.csv nevű mappában található **container2** a **storage2**.

Az R-kód, mutasson a csomópont hivatkozás a **storage2** tárfiók.

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

Az összes a könyvtár- és hivatkozást most mutasson a tárfiók `wasb://container2@storage2.blob.core.windows.net`. Ez a **neve csomópont** megadott.

Meg kell adnia a/User/RevoShare/<SSH username> könyvtárába **storage2** az alábbiak szerint:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-r-server-cluster"></a>Egy Azure Data Lake Store használata R Server-fürt 

Data Lake Store használata a HDInsight-fürthöz, a fürt minden egyes Azure Data Lake Store használni kívánt hozzáférést kell. Az Azure portál használata a HDInsight-fürt létrehozása az alapértelmezett tároló vagy egy további tárolási Azure Data Lake Store-fiókkal, lásd: [HDInsight-fürtök létrehozása az Azure-portál használatával a Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Ezt követően az a tároló az R-parancsfájl a sokkal mint egy másodlagos Azure storage-fiók amilyet az előző eljárásban leírtak szerint.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Az Azure Data Lake-áruházak fürt hozzáférés hozzáadása
A Data Lake store az Azure Active Directory (Azure AD) egyszerű szolgáltatásnév a HDInsight-fürthöz társított használatával éri el.

1. A HDInsight-fürt létrehozásakor válassza ki a **fürt AAD-identitása** a a **adatforrás** fülre.

2. Az a **fürt AAD-identitása** párbeszédpanel **válasszon AD egyszerű**, jelölje be **hozzon létre új**.

Amikor nevezze el az egyszerű szolgáltatás, és hozzon létre egy jelszót az, kattintson a **ADLS-hozzáférés kezelése** a Data Lake Store az egyszerű szolgáltatás hozzárendelni.

Akkor is fürt hozzáférés hozzáadása a fürt létrehozása a következő egy vagy több Data Lake Store fiókot. Nyissa meg a Data Lake Store az Azure portál bejegyzésre, és navigáljon **adatkezelő > hozzáférés > Hozzáadás**. 

### <a name="how-to-access-the-data-lake-store-from-r-server-on-hdinsight"></a>Az R Server on HDInsight a Data Lake store elérése

Után hozzáférést biztosított a Data Lake Store, használhatja a tárban R Server-fürt a HDInsight egy másodlagos Azure storage-fiók módon. Az egyetlen különbség, hogy az előtag **wasb: / /** vált **adl: / /** az alábbiak szerint:


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

Az alábbi parancsokat a Data Lake Store-fiók konfigurálását a RevoShare könyvtárnak, és adja hozzá a minta .csv fájlt az előző példából használhatók:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server-on-hdinsight"></a>Az R Server on HDInsight az Azure File storage használata

Is az adatok tárolási lehetőségként használja az [Azure Files] néven edge csomóponton ((https://azure.microsoft.com/services/storage/files/). Lehetővé teszi egy Azure Storage-megosztás és a Linux fájlrendszer csatlakoztatni. Lehet, hogy ez a beállítás lesz szüksége az adatfájlok, R parancsfájlok és esetleg szükséges később, különösen akkor, ha érdemes a fájlrendszer használatát a HDFS helyett a élcsomópont eredményobjektumok tárolásához. 

A fő Azure fájlok előnye, hogy a fájlmegosztások csatlakoztatott-e, és hogy a rendszer, amely rendelkezik egy támogatott operációs rendszer, például a Windows vagy Linux által használt. Például használhatná, amely rendelkezik a csoport vagy egy másik HDInsight-fürt által, egy Azure virtuális Gépen, vagy akár egy a helyszíni rendszer szerint. További információkért lásd:

- [Az Azure File Storage használata Linuxszal](../../storage/files/storage-how-to-use-files-linux.md)
- [A Windows Azure File storage használata](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>További lépések

* [R Server-fürt a HDInsight áttekintése](r-server-overview.md)
* [Az R Server-fürt hadoop első lépései](r-server-get-started.md)
* [Számítási környezeti beállítások a HDInsighton belüli R Server-fürt esetében](r-server-compute-contexts.md)


---
title: "Az R Server on HDInsight - Azure Azure tárolási megoldások |} Microsoft Docs"
description: "További információk a HDInsight az R Server rendelkező felhasználók számára elérhető más tárolási lehetőségek"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 80af328fc046f8d33727930d02b569da37ccf332
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>Az R Server on HDInsight az Azure tárolási megoldások

Microsoft az R Server on HDInsight számos különféle tárolási megoldásokkal megőrizni az adatokat, kódok vagy elemzés eredményeinek tartalmazó objektumokra. Ezek közé tartozik a következő beállításokat:

- [Az Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Az Azure Data Lake-tároló](https://azure.microsoft.com/services/data-lake-store/)
- [Az Azure File storage](https://azure.microsoft.com/services/storage/files/)

Lehetősége is van a több Azure storage-fiókok vagy tárolók a HDI-fürtnek való hozzáférés. Az Azure File storage az adatok tárolási lehetőség használható a peremhálózati csomóponton, amely lehetővé teszi, hogy csatlakoztassa egy Azure Storage-megosztás és, például a Linux fájlrendszer. Azonban az Azure fájlmegosztások csatlakoztatva, és a rendszer, amely rendelkezik egy támogatott operációs rendszer, például a Windows vagy Linux használja. 

Amikor a HDInsight Hadoop-fürtöt hoz létre, vagy megadhatja egy **az Azure storage** fiók vagy egy **Data Lake store**. Ebből a fiókból adott tárolót a fájlrendszer a fürt létrehozásakor (például a Hadoop elosztott fájlrendszerből) tartalmazza. További információt és útmutatást lásd:

- [Az Azure storage használata a hdinsight eszközzel](../hdinsight-hadoop-use-blob-storage.md)
- [Használjon Data Lake Store az Azure HDInsight-fürtök](../hdinsight-hadoop-use-data-lake-store.md). 

Az Azure tárolási megoldásait további információkért lásd: [Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md). 

A használatára a forgatókönyvéhez leginkább megfelelő tárolási lehetőség kiválasztásával útmutatóért lásd: [való Azure BLOB, Azure-fájlok vagy Azure adatlemezek használata](../../storage/common/storage-decide-blobs-files-disks.md) 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>R Server Azure Blob storage-fiókok használata

Ha szükséges, az Azure storage-fiókok vagy a tárolókat elérheti a HDI-fürthöz. Ehhez meg kell adnia a további tárfiókok a felhasználói felületen, amikor a fürt létrehozása, majd kövesse az alábbi lépéseket az R Server használandó.

> [!WARNING]
> Teljesítmény érdekében a HDInsight-fürt létrehozása a megadott elsődleges tárfiókkal azonos adatközpontba. A storage-fiók egy másik helyen, mint a HDInsight-fürt használata nem támogatott.

1. A tárfiók neve a HDInsight-fürtök létrehozása **storage1** és egy alapértelmezett tároló nevű **container1**.
2. Adjon meg egy további nevű tárfiókot **storage2**.  
3. Másolja a mycsv.csv fájlt a /share könyvtárba, és elvégezhetik az elemzést a fájlhoz.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. Az R-kód beállítása a név csomópont **alapértelmezés szerint** és állítsa be a könyvtár- és feldolgozni.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

A könyvtár- és hivatkozások mutasson a tárfiók wasb://container1@storage1.blob.core.windows.net. Ez a **alapértelmezett tárfiók** , amely a HDInsight-fürthöz van társítva.

Most tegyük fel szeretne dolgozni egy fájlt, amely a /private található mySpecial.csv nevű mappában található **container2** a **storage2**.

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
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Az összes a könyvtár- és hivatkozást most mutasson a tárfiók wasb://container2@storage2.blob.core.windows.net. Ez a **neve csomópont** megadott.

Meg kell adnia a/User/RevoShare/<SSH username> könyvtárába **storage2** az alábbiak szerint:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>R Server az Azure Data Lake store használata

Data Lake-tárolók használata a HDInsight-fiókját, a fürt hozzáférést minden használni kívánt Azure Data Lake store kell. Az Azure portál használata a HDInsight-fürt létrehozása az alapértelmezett tároló vagy egy további tárolási Azure Data Lake Store-fiókkal, lásd: [HDInsight-fürtök létrehozása az Azure-portál használatával a Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Ezt követően az a tároló az R-parancsfájl a sokkal mint egy másodlagos Azure storage-fiók amilyet az előző eljárásban leírtak szerint.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Az Azure Data Lake-áruházak fürt hozzáférés hozzáadása
A Data Lake store az Azure Active Directory (Azure AD) egyszerű szolgáltatásnév a HDInsight-fürthöz társított használatával éri el.

Az Azure AD szolgáltatás egyszerű hozzáadása:

1. A HDInsight-fürt létrehozásakor válassza ki a **fürt AAD-identitása** a a **adatforrás** fülre.

2. Az a **fürt AAD-identitása** párbeszédpanel **válasszon AD egyszerű**, jelölje be **hozzon létre új**.

Amikor nevezze el az egyszerű szolgáltatás, és hozzon létre egy jelszót az, kattintson a **ADLS-hozzáférés kezelése** a szolgáltatás egyszerű társítja a Data Lake tárolja.

Akkor is a fürt létrehozása a következő egy vagy több Data Lake áruházak hozzáférés a fürthöz hozzáadni. Nyissa meg a Data Lake store az Azure portál bejegyzésre, és navigáljon **adatkezelő > hozzáférés > Hozzáadás**. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>A Data Lake store elérése R kiszolgálóról

Után hozzáférést biztosított egy Data Lake Store-ba, használhatja a tárban R Server a HDInsight egy másodlagos Azure storage-fiók módon. Az egyetlen különbség, hogy az előtag **wasb: / /** vált **adl: / /** az alábbiak szerint:


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
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


Az alábbi parancsokat a RevoShare directory konfigurálni a Data Lake-tárfiókot, és adja hozzá a minta .csv fájlt az előző példából használt:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>R Server Azure File storage használata

Is az adatok tárolási lehetőségként használatra a peremhálózati csomóponton néven [Azure fájlok] ((https://azure.microsoft.com/services/storage/files/). Lehetővé teszi egy Azure Storage-megosztás és a Linux fájlrendszer csatlakoztatni. Lehet, hogy ez a beállítás lesz szüksége az adatfájlok, R parancsfájlok és esetleg szükséges később, különösen akkor, ha érdemes a fájlrendszer használatát a HDFS helyett a élcsomópont eredményobjektumok tárolásához. 

A fő Azure fájlok előnye, hogy a fájlmegosztások csatlakoztatott-e, és hogy a rendszer, amely rendelkezik egy támogatott operációs rendszer, például a Windows vagy Linux által használt. Például használhatná, amely rendelkezik a csoport vagy egy másik HDInsight-fürt által, egy Azure virtuális Gépen, vagy akár egy a helyszíni rendszer szerint. További információkért lásd:

- [Az Azure File Storage használata Linuxszal](../../storage/files/storage-how-to-use-files-linux.md)
- [A Windows Azure File storage használata](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Következő lépések

Most, hogy megismerkedett az Azure storage-beállítások, az alábbi hivatkozásokra számára az adatok tudományos feladatok az R Server on HDInsight használata módjait.

* [Az R Server on HDInsight áttekintése](r-server-overview.md)
* [Az R server, a Hadoop első lépései](r-server-get-started.md)
* [Rstudióból kiszolgáló felvétele a HDInsight (Ha nincs hozzáadva a fürt létrehozása során)](r-server-install-r-studio.md)
* [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](r-server-compute-contexts.md)


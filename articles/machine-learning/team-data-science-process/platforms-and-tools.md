---
title: Platformok és az eszközök adattudomány a csapat projektek - Azure |} Microsoft Docs
description: Részletezi, valamint az adatok és analitikák rendelkezésére álló erőforrások Team adatok tudományos folyamat szabványosítása vállalatok ismerteti.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 404e3dd106edf82f4f22e4c6a17987bd0bc51f65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>Platformok és az adatok tudományos csapatprojektek eszközök

A Microsoft felhőalapú vagy a helyi platformok adatok és elemzési szolgáltatások és erőforrások teljes skáláját biztosít. Ahhoz, hogy a végrehajtási adatok tudományos projektben, hatékony és méretezhető telepíthető. Adatok tudományos projektek valósít meg olyan trackable csoportjai útmutatásért verzió vezérelt, és által biztosított együttműködési módot a [Team adatok tudományos folyamat](overview.md) (TDSP).  A személyzet szerepkörök és a kapcsolódó feladatok szabványosításával egy adatok tudományos csapat a folyamatban végrehajtott áttekintését lásd: [Team adatok tudományos folyamat szerepköröket és feladatokat](roles-tasks.md).

Az elérhető adatok tudományos csoportok használatával a TDSP adatok és analitikák szolgáltatások a következők:

- Adatok tudományos virtuális gép (Windows és Linux CentOS)
- A HDInsight Spark-fürtök
- SQL Data Warehouse
- Azure Data Lake
- A HDInsight Hive-fürtök
- Azure File Storage
- SQL Server 2016 R Services

Ez a dokumentum azt röviden erőforrásokat leíró, és adja meg az oktatóanyagok és a TDSP csoportokkal közzétett forgatókönyvek mutató hivatkozásokat tartalmaz. Ezek segítségével megtudhatja, hogyan használhatja őket részletes máris használhatja azokat az intelligens alkalmazásokat hozhatnak létre. Ezeket az erőforrásokat további információk a termék oldalain érhető el. 

## <a name="data-science-virtual-machine-dsvm"></a>Adatok tudományos virtuális gép (DSVM)

Az adatok tudományos virtuális gép Windows és Linux Microsoft által kínált adatok tudományos modellezési és fejlesztési tevékenységek népszerű eszközöket tartalmazza. Eszközök, mint tartalmazza:

- Microsoft R Server Developer Edition 
- Anaconda Python elosztási
- A Python és R Jupyter notebookok 
- A Visual Studio Community Edition Python és R eszközök a Windows / Linux Holdas
- A Windows a Power BI desktop
- SQL Server 2016 Developer kiadásában a Windows / Linux Postgres

Emellett **ML és AI eszközök** CNTK (egy forrás nyissa meg a részletes tanulási eszközkészlet Microsoft), xgboost, mxnet és Vowpal Wabbit, például.

Jelenleg DSVM érhető el **Windows** és **Linux CentOS** operációs rendszerek. Válassza ki a DSVM (Processzormagok száma) és a memória mennyiségét, amely azt tervezi, hogy hajtható végre rajta adatok tudományos projektek igények alapján méretét. 

A Windows kiadását DSVM további információkért lásd: [Microsoft adatok tudományos virtuális gép](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) az Azure piactéren. A DSVM Linux-kiadás, lásd: [Linux adatok tudományos virtuális gép](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Megtudhatja, hogyan hajthat végre a közös adatok tudományos feladatokat a DSVM a hatékony, lásd: [tíz lehetősége van az adatok tudományos virtuális gép](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Az Azure HDInsight Spark-fürtök

Apache Spark egy nyílt forráskódú párhuzamos feldolgozása keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. A Spark program sebességét, a könnyű, valamint a kifinomult analytics lett tervezve. A Spark memóriában tárolt számítási képességei jól funkcionálnak a machine learning iteratív algoritmusaival és a graph számítások. Spark is az Azure Blob storage (WASB) szolgáltatással kompatibilis, így a meglévő Azure-ban tárolt adatok könnyen dolgozhatók Spark használatával.

Amikor Spark-fürtöt hoz létre a HDInsightban, olyan Azure számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. Hdinsight Spark-fürt létrehozása nagyjából 10 percet vesz igénybe. Az Azure Blob storage feldolgozandó adatok tárolására. Az Azure Blob Storage használata a fürt további információkért lásd: [használatát a HDFS-kompatibilis Azure Blob storage hadooppal a Hdinsightban](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP csapat a Microsoft tett közzé két végpont forgatókönyvek adatok tudományos megoldások létrehozásához Azure HDInsight Spark-fürtök használatáról Python és az egyéb Scala egy használatával. További információ az Azure HDInsight **Spark-fürtjei**, lásd: [– áttekintés: Apache Spark on HDInsight Linux rendszeren](../../hdinsight/spark/apache-spark-overview.md). Megtudhatja, hogyan hozhat létre egy tudományos megoldás használatával végzett **Python** az Azure HDInsight Spark-fürtöt, tekintse meg a [áttekintése adatok tudományos Spark on Azure HDInsight használatának](spark-overview.md). Megtudhatja, hogyan hozhat létre egy tudományos megoldás használatával végzett **Scala** az Azure HDInsight Spark-fürtöt, tekintse meg a [Scala és Spark az Azure használatával Adattudomány](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az SQL Data Warehouse lehetővé teszi a számítási erőforrások egyszerűen és méretezhető (másodpercben), túlzott fizető, vagy túlzott kiosztása nélkül. Az egyedi a beállítást a számítási erőforrások, felkínálva a szabadságot, hogy jobb kezelése érdekében a felhő költségek is biztosít. Méretezhető számítási erőforrások telepítése lehetővé teszi az adatok az Azure SQL Data Warehouse érdekében. Tárolási költségek minimálisak, és futtathatja számítási csak az elemezni kívánt adatkészletek részeit. 

Azure SQL Data Warehouse további információkért tekintse meg a [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webhelyet. Megtudhatja, hogyan hozhat létre az SQL Data Warehouse-végpontok közötti speciális elemzési megoldásairól, lásd: [a csapat adatok tudományos folyamat működés közben: az SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Az Azure data lake van, mint egy vállalati szintű tárház adatgyűjtés egyetlen helyen, bármely bevonja vagy a séma éppen bevezetett előtt minden típusú. Erre a rugalmasságra lehetővé minden típusú adatok a data lake, függetlenül annak méretét vagy a struktúra vagy okozhatnak milyen gyorsan kell tartani. A szervezetek használhatja Hadoop, vagy a ezeket az adatokat mintákra található speciális elemzésekre tavakat. Adatok tavakat is működhetnek alacsonyabb költségű adatok előkészítése előtt az adatok a felfedezhetőséget és áthelyezni, ha az egy data warehouse-tárházat.

További információ az Azure Data Lake: [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Megtudhatja, hogyan hozhat létre Azure Data Lake egy méretezhető végpont adatok tudományos megoldást, lásd: [Azure Data Lake méretezhető Adattudomány: egy végpont forgatókönyv](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Az Azure HDInsight Hive (Hadoop) fürtök

A Hadoop, amely lehetővé teszi a adatainak összefoglalója, lekérdezését és a HiveQL, hasonló SQL lekérdezésnyelvet használatával adatelemzési adatraktárrendszer Apache Hive. Hive interaktív módon az adatokba, vagy újrafelhasználható kötegelt feldolgozási feladatok létrehozására használható.

Hive lehetővé teszi a nagy mértékben strukturálatlan adatok szerkezetének. A struktúra meghatározása után használhatja a Hive nélkül használja, vagy nem ismert, Java vagy MapReduce a Hadoop fürtök tárolt adatokat lekérdezni. HiveQL (a Hive query language) lehetővé teszi az utasításokat, amelyek hasonlóak a T-SQL-lekérdezések írása.

Adatszakértőkön, a Hive futtathat Python User-Defined funkciókat (UDF) rekordok feldolgozásához Hive-lekérdezéseket. Ez a lehetőség jelentősen kiterjeszti az adatok elemzése a Hive-lekérdezések képességet. Pontosabban, lehetővé teszi a méretezhető szolgáltatás mérnöki legtöbbször nem ismeri nyelvű elvégzésére adatszakértőkön: az SQL-szerű HiveQL és a Python. 

Azure HDInsight Hive fürtökön további információkért lásd: [használata Hive és a HiveQL hadooppal a Hdinsightban](../../hdinsight/hadoop/hdinsight-use-hive.md). Az Azure HDInsight Hive fürtök egy méretezhető végpont adatok tudományos megoldás kiépítését, lásd: [a csapat adatok tudományos folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Az Azure File Storage egy olyan szolgáltatás, a felhőben, a standard Server Message Block (SMB) protokollt használó fájlmegosztások által. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. Az Azure virtuális gépeken vagy felhőszolgáltatásában, esetleg helyszíni ügyfeleken üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Különösen hasznos az adatok tudományos projektek azt a képességet hozzon létre egy Azure fájltároló projekt adatok megosztása a projekt csapattagok helyként. Azok majd az az Azure file storage az adatok ugyanazon példányát hozzáféréssel rendelkezik. A fájltároló ahhoz, hogy a projekt végrehajtása során létrehozott szolgáltatáskészletek szolgáltatást is alkalmazhatja. Ha a projekt egy ügyfél engagement, az ügyfelek számára a saját Azure-előfizetéshez a projekt adatok és a szolgáltatások megosztott Önnel egy az Azure file storage hozhat létre. Így az ügyfél rendelkezik teljes körű hozzáférést engedélyezzenek a projekt adategységeket. További információ az Azure File Storage: [Ismerkedés az Azure File storage on Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) és [Azure File Storage használata Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

(Az adatbázis-) R biztosítanak a platform fejlesztéséhez és telepítéséhez is nyújt új betekintést intelligens alkalmazásokat. A hatékony és erőteljes R nyelvi, többek között a csomagok számát az R Közösség által biztosított modellek létrehozásához, és hozza létre az SQL Server-adatok előrejelzéseket használhatja. R szolgáltatások (az adatbázis-) az R nyelv integrálható az SQL Server, mert a analytics megközelíti az adatokat, így a költségeket és a biztonsági kockázatok megköveteli az adatok nem tartanak.

R-szolgáltatások (az adatbázis-) támogatja az SQL Server-eszközök és technológiák átfogó készlete nyílt forráskódú R nyelvét. Kiváló teljesítmény, biztonságra, megbízhatóságra és kezelhetőségre biztosítanak. R megoldások kényelmes és ismerős eszközökkel is telepíthet. Az üzemi alkalmazások adhatja az R-futtatókörnyezet, és előrejelzéseket és a látványelemek Transact-SQL használatával. A méretezési és az R-megoldások teljesítményének javítása érdekében használja a ScaleR könyvtárak is. További információkért lásd: [SQL Server R szolgáltatások](https://msdn.microsoft.com/library/mt604845.aspx)

A TDSP csapat a Microsoft tett közzé két végpont forgatókönyvek, amelyek bemutatják, hogyan hozhat létre a tudományos megoldás az SQL Server 2016 R Servicesben: egyet az R-t használó programozók és egy SQL-fejlesztőknek. A **R-t használó programozók**, lásd: [Adattudomány végpont forgatókönyv](https://msdn.microsoft.com/library/mt612857.aspx). A **SQL fejlesztők**, lásd: [SQL-fejlesztőknek (Útmutató) adatbázis-Advanced Analytics](https://msdn.microsoft.com/library/mt683480.aspx).


## <a name="appendix"></a>A függelék: Eszközök adatok tudományos projektek beállítása

### <a name="install-git-credential-manager-on-windows"></a>A Windows Git hitelesítőadat-kezelő telepítése

Ha a TDSP következő **Windows**, telepítenie kell a **Git hitelesítőadat-kezelő (GCM)** a Git adattárak folytatott kommunikációhoz. GCM telepítéséhez először telepítendő **Chocolaty**. Chocolaty és a GCM telepítéséhez futtassa a következő parancsokat a Windows Powershellt, egy **rendszergazda**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Telepítse a Git gépeken Linux (CentOS)

A következő parancsot bash Git telepítése Linux (CentOS) gépeken:

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Linux (CentOS) gépeken nyilvános SSH-kulcs létrehozása

Linux (CentOS) gépek használatakor a git-parancsok futtatásához kell hozzáadnia a nyilvános SSH-kulcs a számítógép a VSTS-kiszolgálóhoz, hogy a VSTS-kiszolgáló felismeri ezen a számítógépen. Először meg kell hozzon létre egy nyilvános SSH-kulcsot, és a kulcs hozzáadása a VSTS biztonsági beállításait tartalmazó lapra a nyilvános SSH-kulcsok. 

- Az SSH-kulcs létrehozásához futtassa az alábbi két parancsot: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Másolja a teljes ssh kulcs beleértve *ssh-rsa*. 
- Jelentkezzen be a VSTS-kiszolgálóra. 
- Kattintson a **< név\>**  a lapon, majd kattintson a jobb felső sarokban, **biztonsági**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Kattintson a **nyilvános SSH-kulcsok**, és kattintson a **+ Hozzáadás**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Beillesztés az ssh kulcs legutóbb másolt a szövegmezőbe, és mentse el.


## <a name="next-steps"></a>További lépések

Végpont forgatókönyvek, amelyek azt mutatják, a folyamat lépései teljes **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) témakör. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Az adatok tudományos folyamatban lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban, tekintse meg a [Azure ML](http://aka.ms/datascienceprocess) képzési.
---
title: Platformok és eszközök a adatelemzési projektek - csoportos adatelemzési folyamat
description: Elemenként mutatja be, és a csoportos adatelemzési folyamat a szabványosításával vállalatok számára rendelkezésre álló adatok és analitika forrásokat ismerteti.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f733b2c9bcd8dc8ad8c068c465e1beda00b45d28
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471011"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformok és eszközök a adatelemzési projektek

A Microsoft cloud vagy a helyszíni rendszerek egy teljes spektruma elérhető data és analitikai szolgáltatásokat és erőforrásokat kínál. Győződjön meg arról, az adatelemzési projektek végrehajtásának hatékony és méretezhető telepíthetők. Útmutató a végrehajtási adatelemzési projektek egy trackable csapatok, verzió ellenőrzött, és remek biztosítja a [csoportos adatelemzési folyamat](overview.md) (TDSP).  A személyzet szerepkörök és hozzájuk kapcsolódó részfeladatokat szabványosításával egy data science csapat ezt a folyamatot a kezelt áttekintését lásd: [csoportos adatelemzési folyamat szerepkörök és feladatok](roles-tasks.md).

A rendelkezésre álló adatok adattudománnyal foglalkozó csapatai TDSP használata az adatok és analitika szolgáltatások a következők:

- Adatelemző virtuális gépek (Windows és Linux CentOS)
- HDInsight Spark-fürtök
- SQL Data Warehouse
- Azure Data Lake
- Hive a HDInsight-fürtök
- Azure File Storage
- SQL Server 2016 R Services

Ez a dokumentum azt röviden ismertetik az erőforrások és oktatóanyagok és útmutatók a TDSP csapatok közzétette mutató hivatkozásokat tartalmaznak. Ezek segítségével megtudhatja, hogyan használhat lépésről lépésre, és azokat az intelligens alkalmazásokat hozhat létre használatának megkezdéséhez. Ezeket az erőforrásokat a további információk termék oldalakon érhetők el. 

## <a name="data-science-virtual-machine-dsvm"></a>Adatelemző virtuális gép (DSVM)

A Windows és a Linux, a Microsoft által kínált az adatelemző virtuális gép az adatelemzési modellezéshez és fejlesztéshez tevékenységeket népszerű eszközöket tartalmaz. Eszközök például tartalmazza:

- A Microsoft R Server Developer Edition 
- Anaconda Python elosztási
- A Jupyter notebooks, a Python- és R 
- A Visual Studio Community Edition kiadást a Python és az R-eszközök a Windows / Linux Eclipse
- A Windows a Power BI desktopban
- Az SQL Server 2016 Developer Edition verziót a Windows / Linux Postgres

Ezenkívül **gépi Tanulási és AI-eszközök** CNTK (egy nyílt forráskódú Mélytanulási eszközkészlet a Microsoft), illetve xgboost, mxnet és Vowpal Wabbit, például.

Dsvm-hez jelenleg elérhető **Windows** és **Linux CentOS** operációs rendszereket. Válassza ki a dsvm-hez (a Processzormagok száma) és a memória mennyiségét, amelyre hajthat végre, az adatelemzési projektek igényeinek megfelelően méretét. 

A DSVM Windows-kiadását további információkért lásd: [Microsoft Data Science virtuális gép](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) az Azure marketplace-en. A DSVM Linux-kiadását, lásd: [Linux rendszerű adatelemző virtuális gép](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Megtudhatja, hogyan hajthat végre a gyakori adatelemzési feladatok némelyikét a DSVM hatékonyan, lásd: [tíz dolog, amire alkalmas az adatelemző virtuális gép](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Az Azure HDInsight Spark-fürtök

Az Apache Spark egy nyílt forráskódú párhuzamos adatfeldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriában tárolt számítási képességei győződjön meg arról, hogy a jó választás, és a graph számítások iteratív algoritmusaival együtt a machine Learning szolgáltatásban. A Spark az is kompatibilis Azure Blob storage (WASB), a, így az Azure-ban tárolt meglévő adatok is a Spark használatával.

Amikor Spark-fürtöt hoz létre a HDInsightban, olyan Azure számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. A HDInsight Spark-fürt létrehozása nagyjából 10 percet tart. Store az adatok Azure Blob storage-ban a feldolgozásra. Az Azure Blob Storage használata a fürt további információkért lásd: [használja a HDFS-kompatibilis Azure Blob storage a HDInsight Hadoop-keretrendszerrel](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP-csapat a Microsoft közzétette az adatelemzési megoldások, Azure HDInsight Spark-fürtök használatával két végpontok közötti forgatókönyvek közül egy a Python és az egyéb Scala. További információ az Azure HDInsight **Spark-fürtök**, lásd: [áttekintése: Az Apache Spark on HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Megtudhatja, hogyan hozhat létre egy data science megoldás használatával **Python** az Azure HDInsight Spark-fürt látható [áttekintése az adatelemzés az Azure HDInsight Spark használatával](spark-overview.md). Megtudhatja, hogyan hozhat létre egy data science megoldás használatával **Scala** az Azure HDInsight Spark-fürt látható [Scala és Spark használata az Azure Data Science](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az Azure SQL Data Warehouse lehetővé teszi a számítási erőforrások egyszerűen és skálázását (másodpercben), túlterhelt túltelepítés és túlfizetés nélkül. Szüneteltetés használt számítási erőforrások, így a felhőköltségek kontrollja szabadon egyedülálló lehetőséget is kínál. Méretezhető számítási erőforrások üzembe helyezése lehetővé teszi az összes adatot betöltheti a az Azure SQL Data Warehouse lehetővé teszi. Tárolási költségek minimálisak, és futtathatja számítási csak azon részei, amely az elemezni kívánt adatkészlet. 

Az Azure SQL Data Warehouse további információkért lásd: a [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webhelyén. Az SQL Data Warehouse teljes körű, fejlett elemzési megoldásokat hozhat létre, lásd: [a csoportos adatelemzési folyamat működés közben: az SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Az Azure data lake van, mint egy vállalati szintű adattár, bármilyen típusú adat egyetlen helyen, bármely bevonja vagy a séma éppen bevezetett előtt gyűjteni. Ez rugalmasságot lehetővé teszi, hogy bármilyen típusú adat a data lake, függetlenül a méret vagy struktúra, vagy milyen gyors adatbetöltés sebességétől kell tartani. Szervezetek használhatják majd a Hadoop, vagy keresse meg a speciális analitikai ezeket az adatokat Lake mintáit. Data Lake tárolók segédanyagként is használható vagy a alacsonyabb költségű adatelőkészítés előtt a felfedezhetőséget az adatokat, és áthelyezi a data warehouse-bA.

További információ az Azure Data Lake: [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Ismerje meg, hogyan hozhat létre egy méretezhető teljes körű adatelemzési megoldással az Azure Data Lake, lásd: [méretezhető adatelemzés az Azure Data Lake: Egy végpontok közötti forgatókönyv](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Az Azure HDInsight Hive (Hadoop) fürtön

Az Apache Hive-egy rendszer data warehouse hadoophoz, amely adatösszegzéseket, lekérdezéséhez és a HiveQL, egy hasonló, az SQL-lekérdezési nyelv használata az adatok elemzése lehetővé teszi. Hive használható az adatok interaktív feltárása, vagy hozhat létre újrahasznosítható kötegelt feldolgozás feladatokat.

Hive teszi projektstruktúra nagymértékben strukturálatlan adatokon. Struktúráját határozza meg, miután a Hive használatával, vagy még akkor is tudom, Java vagy a MapReduce nélkül kérdezheti le az adatokat a Hadoop-fürt. HiveQL (a Hive query language) lehetővé teszi az utasításokat, amelyek hasonlóak a T-SQL lekérdezéseket írni.

Adatszakértők Hive Python User-Defined függvények (UDF-EK) futtathat a Hive-lekérdezések rekordok feldolgozásához. Ez a képesség jelentősen kibővíti az adatok elemzése a Hive-lekérdezések képességét. Pontosabban, lehetővé teszi az adatszakértők megalapozottságát biztosító előzetes tájékozódást méretezhető funkciófejlesztési nyelvű többnyire nem ismeri: az SQL-szerű HiveQL- és Python. 

Az Azure HDInsight Hive-fürtök további információkért lásd: [Hive és a hadooppal a HDInsight HiveQL](../../hdinsight/hadoop/hdinsight-use-hive.md). Ismerje meg, hogyan hozhat létre egy méretezhető teljes körű adatelemzési megoldás az Azure HDInsight Hive-fürtöket, lásd: [a csoportos adatelemzési folyamat működés közben: HDInsight Hadoop-fürtöket használó](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Az Azure File Storage olyan szolgáltatás, a felhőben, a szabványos Server Message Block (SMB) protokollt használó fájlmegosztások. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. Az Azure virtuális gépeken vagy felhőszolgáltatásában, esetleg helyszíni ügyfeleken üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Adatelemzési projektek különösen hasznos a rendszer azon képessége, egy Azure-beli tároló létrehozása, a hely a projekt adatok megosztása a projekt csapatával. Mindegyik majd hozzáféréssel rendelkezik az adatok egyazon példányát az Azure file storage-ban. Ez a file storage a projekt végrehajtása során létrehozott szolgáltatáskészleteket megosztása szolgáltatást is alkalmazhatja. Ha a projekt egy ügyfél engagement, az ügyfelek egy az Azure file storage a saját Azure-előfizetéshez megosztani a projekt adatokat és szolgáltatásokat hozhat létre. Így az ügyfél a projekt adategységek teljes hozzáféréssel rendelkezik. További információ az Azure File Storage: [a Windows Azure File storage használatának első lépései](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) és [Azure File Storage használata linuxszal](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services (adatbázison belüli) adja meg a platform fejlesztéséhez és üzembe helyezéséhez intelligens alkalmazásokat, amelyek új elemzéseket is. Használhatja a sokoldalú, hatékony R nyelven, ideértve például az R-Közösség által biztosított csomagok számát modelleket hozhat létre és előrejelzések készítése az SQL Server-adatok alapján. R Services (adatbázison belüli) integrálható az SQL Server az R nyelvvel, mert analytics közel az adatokat, így a költségek és az adatok áthelyezését a kapcsolódó biztonsági kockázatok nem őrzi meg.

R Services (adatbázison belüli) támogatja a nyílt forráskódú R nyelvvel, az SQL Server-eszközök és technológiák átfogó készletével. Kiváló teljesítmény, biztonság, megbízhatóság és kezelhetőségi kínálnak. R-alapú megoldások kényelmes és ismerős eszközök használatával is telepítheti. Az éles alkalmazások hívja az R futtatókörnyezettel és előrejelzéseket és a Vizualizációk Transact-SQL használatával. Is használhatja a ScaleR-kódtárakat a méretezési csoport és az R-alapú megoldások teljesítményének javítása érdekében. További információkért lásd: [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

A TDSP csapat a Microsoft tett közzé két végpontok közötti forgatókönyvek, amelyek bemutatják az SQL Server 2016 R Services adatelemzési megoldások: egy R-programozók, egy, az SQL-fejlesztők számára. A **R-programozók**, lásd: [Data Science végpontok közötti forgatókönyv](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). A **SQL fejlesztők**, lásd: [(oktatóanyag) fejlesztőknek SQL adatbázis-Advanced Analytics](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>A függelék: Eszközök adatelemzési projektek beállítása

### <a name="install-git-credential-manager-on-windows"></a>A Git Credential Manager telepíthető Windows

Ha az a TDSP **Windows**, telepítenie kell a **Git Credential Manager (GCM)** kommunikálni a Git-tárházakat. GCM telepíteni, először telepítenie kell **Chocolaty**. Chocolaty és a GCM telepítéséhez futtassa az alábbi parancsokat a Windows Powershellt, egy **rendszergazda**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>A Git telepítése (CentOS) Linux rendszerű gépeken

A következő paranccsal bash Git telepítéséhez (CentOS) Linuxos gépeken:

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>(CentOS) Linuxos gépeken a nyilvános SSH-kulcs létrehozása

(CentOS) Linux-gépek használatakor a git-parancsok futtatásához szüksége a gép a nyilvános SSH-kulcs hozzáadása az Azure DevOps-szolgáltatásokkal, hogy a gép már ismeri fel az Azure DevOps-szolgáltatásokkal. Először hozza létre a nyilvános SSH-kulcsot, és adja hozzá a kulcsot az Azure DevOps-szolgáltatásokkal biztonsági beállítások oldalon nyilvános SSH-kulcsokat. 

- Az SSH-kulcs létrehozásához futtassa az alábbi két parancsot: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![Parancsokat az SSH-kulcs létrehozása](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Másolja a teljes ssh kulcs beleértve *ssh-rsa*. 
- Jelentkezzen be az Azure DevOps-szolgáltatásokkal. 
- Kattintson a **< név\>**  a lapot, majd kattintson a jobb felső sarokban található **biztonsági**. 
    
    ![Kattintson a nevére, és kattintson a biztonsági](./media/platforms-and-tools/resources-2-user-setting.png)

- Kattintson a **nyilvános SSH-kulcsokat**, és kattintson a **+ Hozzáadás**. 

    ![Kattintson a nyilvános SSH-kulcsokat, és majd kattintson a + Hozzáadás](./media/platforms-and-tools/resources-3-add-ssh.png)

- Illessze be az ssh kulcs imént másolt a szövegmezőbe, és mentse el.


## <a name="next-steps"></a>További lépések

Teljes, végpontok közötti forgatókönyvek, amelyek bemutatják, a folyamat összes lépését **meghatározott forgatókönyvek** is rendelkezésre állnak. Felsorolt, és a miniatűr leírásokat tartalmazó társított a [példa forgatókönyvek](walkthroughs.md) témakör. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

Példák a csoportos adatelemzési folyamat a lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban az [az Azure ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) képzési terv.

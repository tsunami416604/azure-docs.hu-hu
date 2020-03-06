---
title: Platformok és eszközök a adatelemzési projektek - csoportos adatelemzési folyamat
description: Elemenként mutatja be, és a csoportos adatelemzési folyamat a szabványosításával vállalatok számára rendelkezésre álló adatok és analitika forrásokat ismerteti.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3297319c67ad2b7c94371356cde49113c7ef737
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391290"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformok és eszközök a adatelemzési projektek

A Microsoft a felhőalapú és a helyszíni platformokhoz egyaránt teljes körű elemzési erőforrásokat biztosít. Győződjön meg arról, az adatelemzési projektek végrehajtásának hatékony és méretezhető telepíthetők. Útmutató az adatelemzési projektek nyomon követésére szolgáló, ellenőrzött, és együttműködési módon megvalósított csapatok számára a [csoportos adatelemzési folyamat](overview.md) (TDSP) révén.  A munkatársak szerepköreinek, valamint a folyamaton alapuló adatelemzési csapat által kezelt kapcsolódó feladatoknak a vázlatát lásd: [csoportos adatelemzési folyamat szerepkörei és feladatai](roles-tasks.md).

A TDSP használó adatelemző csapatok számára elérhető elemzési erőforrások a következők:

- Adatelemző virtuális gépek (Windows és Linux CentOS)
- HDInsight Spark-fürtök
- Synapse Analytics
- Azure Data Lake
- Hive a HDInsight-fürtök
- Azure File Storage
- SQL Server 2019 R és Python szolgáltatások
- Azure Databricks

Ez a dokumentum azt röviden ismertetik az erőforrások és oktatóanyagok és útmutatók a TDSP csapatok közzétette mutató hivatkozásokat tartalmaznak. Ezek segítségével megtudhatja, hogyan használhat lépésről lépésre, és azokat az intelligens alkalmazásokat hozhat létre használatának megkezdéséhez. Ezeket az erőforrásokat a további információk termék oldalakon érhetők el. 

## <a name="data-science-virtual-machine-dsvm"></a>Adatelemző virtuális gép (DSVM)

A Windows és a Linux, a Microsoft által kínált az adatelemző virtuális gép az adatelemzési modellezéshez és fejlesztéshez tevékenységeket népszerű eszközöket tartalmaz. Eszközök például tartalmazza:

- A Microsoft R Server Developer Edition 
- Anaconda Python elosztási
- A Jupyter notebooks, a Python- és R 
- A Visual Studio Community Edition kiadást a Python és az R-eszközök a Windows / Linux Eclipse
- A Windows a Power BI desktopban
- Az SQL Server 2016 Developer Edition verziót a Windows / Linux Postgres

Ide tartozik a **ml és AI-eszközök** is, például a xgboost, a mxnet és a Vowpal Wabbit.

Jelenleg a DSVM a **Windows** és a **Linux CentOS** operációs rendszerekben érhető el. Válassza ki a dsvm-hez (a Processzormagok száma) és a memória mennyiségét, amelyre hajthat végre, az adatelemzési projektek igényeinek megfelelően méretét. 

A DSVM Windows-kiadásával kapcsolatos további információkért lásd: [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) az Azure piactéren. A DSVM Linux-kiadását lásd: [linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Ha szeretné megtudni, hogyan hajthat végre a közös adatelemzési feladatokat a DSVM hatékonyan, tekintse meg [az adatelemzési virtuális gépen 10 dolgot](../data-science-virtual-machine/vm-do-ten-things.md) .


## <a name="azure-hdinsight-spark-clusters"></a>Az Azure HDInsight Spark-fürtök

Az Apache Spark egy nyílt forráskódú párhuzamos adatfeldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriában tárolt számítási képességei győződjön meg arról, hogy a jó választás, és a graph számítások iteratív algoritmusaival együtt a machine Learning szolgáltatásban. A Spark az is kompatibilis Azure Blob storage (WASB), a, így az Azure-ban tárolt meglévő adatok is a Spark használatával.

Amikor Spark-fürtöt hoz létre a HDInsightban, olyan Azure számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. A HDInsight Spark-fürt létrehozása nagyjából 10 percet tart. Store az adatok Azure Blob storage-ban a feldolgozásra. Az Azure Blob Storage fürttel való használatáról további információért lásd: a [HDFS-kompatibilis Azure Blob Storage használata a Hadoop a HDInsight-ben](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP-csapat a Microsoft közzétette az adatelemzési megoldások, Azure HDInsight Spark-fürtök használatával két végpontok közötti forgatókönyvek közül egy a Python és az egyéb Scala. Az Azure HDInsight **Spark-fürtökkel**kapcsolatos további információkért tekintse [meg a HDInsight Linux – Áttekintés: Apache Spark](../../hdinsight/spark/apache-spark-overview.md)című témakört. Ha szeretné megismerni, hogyan hozhat létre adatelemzési megoldást a **Python** használatával egy Azure HDInsight Spark-fürtön, tekintse meg [az adatelemzés áttekintése a Spark használatával az Azure HDInsight](spark-overview.md)című cikket. Ha meg szeretné tudni, hogyan hozhat létre adatelemzési megoldást a **Scala** használatával egy Azure HDInsight Spark-fürtön, tekintse meg az [adatelemzést a Scala és a Spark Azure-beli használatával](scala-walkthrough.md)című 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az Azure SQL Data Warehouse lehetővé teszi a számítási erőforrások egyszerűen és skálázását (másodpercben), túlterhelt túltelepítés és túlfizetés nélkül. Szüneteltetés használt számítási erőforrások, így a felhőköltségek kontrollja szabadon egyedülálló lehetőséget is kínál. Méretezhető számítási erőforrások üzembe helyezése lehetővé teszi az összes adatot betöltheti a az Azure SQL Data Warehouse lehetővé teszi. Tárolási költségek minimálisak, és futtathatja számítási csak azon részei, amely az elemezni kívánt adatkészlet. 

Azure SQL Data Warehouseről a [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webhelyén talál további információt. Ha szeretné megtudni, hogyan hozhat létre teljes körű fejlett elemzési megoldásokat SQL Data Warehouseekkel, tekintse meg [a csoportos adatelemzési folyamat működés közben: SQL Data Warehouse használata](sqldw-walkthrough.md)című témakört.


## <a name="azure-data-lake"></a>Azure Data Lake

A Azure Data Lake a formális követelmények vagy a szükséges sémák megkezdése előtt egyetlen helyen gyűjtött minden típusú adat vállalati szintű tárháza. Ez rugalmasságot lehetővé teszi, hogy bármilyen típusú adat a data lake, függetlenül a méret vagy struktúra, vagy milyen gyors adatbetöltés sebességétől kell tartani. Szervezetek használhatják majd a Hadoop, vagy keresse meg a speciális analitikai ezeket az adatokat Lake mintáit. Data Lake tárolók segédanyagként is használható vagy a alacsonyabb költségű adatelőkészítés előtt a felfedezhetőséget az adatokat, és áthelyezi a data warehouse-bA.

További információ a Azure Data Lakeről: [Azure Data Lake bemutatása](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Ha szeretné megismerni, hogyan hozhat létre méretezhető, teljes körű adatelemzési megoldást Azure Data Lakekal, tekintse meg a [skálázható adatelemzést a Azure Data Lakeban: teljes körű útmutató](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Az Azure HDInsight Hive (Hadoop) fürtön

Az Apache Hive-egy rendszer data warehouse hadoophoz, amely adatösszegzéseket, lekérdezéséhez és a HiveQL, egy hasonló, az SQL-lekérdezési nyelv használata az adatok elemzése lehetővé teszi. Hive használható az adatok interaktív feltárása, vagy hozhat létre újrahasznosítható kötegelt feldolgozás feladatokat.

Hive teszi projektstruktúra nagymértékben strukturálatlan adatokon. Struktúráját határozza meg, miután a Hive használatával, vagy még akkor is tudom, Java vagy a MapReduce nélkül kérdezheti le az adatokat a Hadoop-fürt. HiveQL (a Hive query language) lehetővé teszi az utasításokat, amelyek hasonlóak a T-SQL lekérdezéseket írni.

Adatszakértők Hive Python User-Defined függvények (UDF-EK) futtathat a Hive-lekérdezések rekordok feldolgozásához. Ez a képesség jelentősen kibővíti az adatok elemzése a Hive-lekérdezések képességét. Pontosabban, lehetővé teszi az adatszakértők megalapozottságát biztosító előzetes tájékozódást méretezhető funkciófejlesztési nyelvű többnyire nem ismeri: az SQL-szerű HiveQL- és Python. 

Az Azure HDInsight kaptár-fürtökkel kapcsolatos további információkért lásd: a [kaptár és a HiveQL használata a Hadoop-ben a HDInsight-ben](../../hdinsight/hadoop/hdinsight-use-hive.md). A teljes körű adatelemzési megoldás Azure HDInsight kaptár-fürtökkel való kiépítéséről [a csoportos adatelemzési folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md)című témakörben olvashat.


## <a name="azure-file-storage"></a>Azure File Storage 

Az Azure File Storage olyan szolgáltatás, a felhőben, a szabványos Server Message Block (SMB) protokollt használó fájlmegosztások. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. Az Azure virtuális gépeken vagy felhőszolgáltatásában, esetleg helyszíni ügyfeleken üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Adatelemzési projektek különösen hasznos a rendszer azon képessége, egy Azure-beli tároló létrehozása, a hely a projekt adatok megosztása a projekt csapatával. Mindegyik majd hozzáféréssel rendelkezik az adatok egyazon példányát az Azure file storage-ban. Ez a file storage a projekt végrehajtása során létrehozott szolgáltatáskészleteket megosztása szolgáltatást is alkalmazhatja. Ha a projekt egy ügyfél engagement, az ügyfelek egy az Azure file storage a saját Azure-előfizetéshez megosztani a projekt adatokat és szolgáltatásokat hozhat létre. Így az ügyfél a projekt adategységek teljes hozzáféréssel rendelkezik. További információ az Azure File Storage-ról: Ismerkedés [Az Azure file Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) szolgáltatással Windows rendszeren és az [Azure file Storage használata Linux](../../storage/files/storage-how-to-use-files-linux.md)rendszeren.


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R és Python szolgáltatások

Az R Services (in-Database) platformot biztosít olyan intelligens alkalmazások fejlesztéséhez és üzembe helyezéséhez, amelyek új ismereteket tudnak feltárni. Használhatja a sokoldalú, hatékony R nyelven, ideértve például az R-Közösség által biztosított csomagok számát modelleket hozhat létre és előrejelzések készítése az SQL Server-adatok alapján. Mivel az R Services (in-Database) az R nyelvet integrálja SQL Serverokkal, az elemzések az adathoz közelednek, ami kiküszöböli az adatáthelyezéshez kapcsolódó költségeket és biztonsági kockázatokat.

Az r Services (in-Database) támogatja a nyílt forráskódú R nyelvet SQL Server eszközök és technológiák átfogó készletével. Kiváló teljesítmény, biztonság, megbízhatóság és kezelhetőségi kínálnak. R-alapú megoldások kényelmes és ismerős eszközök használatával is telepítheti. Az éles alkalmazások hívja az R futtatókörnyezettel és előrejelzéseket és a Vizualizációk Transact-SQL használatával. Is használhatja a ScaleR-kódtárakat a méretezési csoport és az R-alapú megoldások teljesítményének javítása érdekében. További információ: [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

A TDSP csapat a Microsoft tett közzé két végpontok közötti forgatókönyvek, amelyek bemutatják az SQL Server 2016 R Services adatelemzési megoldások: egy R-programozók, egy, az SQL-fejlesztők számára. **R-programozók**esetében lásd: [adatelemzési végpontok közötti útmutató](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). **SQL-fejlesztők**számára lásd: [az adatbázishoz készült Advanced Analytics for SQL Developers (oktatóanyag)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Függelék: az adatelemzési projektek beállításához szükséges eszközök

### <a name="install-git-credential-manager-on-windows"></a>A Git Credential Manager telepíthető Windows

Ha a **Windows**TDSP követi, telepítenie kell a **git hitelesítőadat-kezelőt (GCM)** a git-adattárakkal való kommunikációhoz. A GCM telepítéséhez először a **chocolatt**kell telepítenie. A Chocolat és a GCM telepítéséhez futtassa a következő parancsokat a Windows PowerShellben **rendszergazdaként**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>A Git telepítése (CentOS) Linux rendszerű gépeken

A következő paranccsal bash Git telepítéséhez (CentOS) Linuxos gépeken:

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>(CentOS) Linuxos gépeken a nyilvános SSH-kulcs létrehozása

(CentOS) Linux-gépek használatakor a git-parancsok futtatásához szüksége a gép a nyilvános SSH-kulcs hozzáadása az Azure DevOps-szolgáltatásokkal, hogy a gép már ismeri fel az Azure DevOps-szolgáltatásokkal. Először hozza létre a nyilvános SSH-kulcsot, és adja hozzá a kulcsot az Azure DevOps-szolgáltatásokkal biztonsági beállítások oldalon nyilvános SSH-kulcsokat. 

1. Az SSH-kulcs létrehozásához futtassa az alábbi két parancsot: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Parancsokat az SSH-kulcs létrehozása](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Másolja a teljes SSH-kulcsot, beleértve az *SSH-RSA-* t. 
1. Jelentkezzen be az Azure DevOps-szolgáltatásokkal. 
1. Kattintson a lap jobb felső sarkában található **<\>nevére** , majd a **Biztonság**elemre. 
    
   ![Kattintson a nevére, és kattintson a biztonsági](./media/platforms-and-tools/resources-2-user-setting.png)

1. Kattintson a **nyilvános ssh-kulcsok**elemre, majd kattintson a **+ Hozzáadás**gombra. 

   ![Kattintson a nyilvános SSH-kulcsokat, és majd kattintson a + Hozzáadás](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Illessze be a szövegmezőbe másolt SSH-kulcsot, és mentse a fájlt.


## <a name="next-steps"></a>Következő lépések

Teljes körű útmutatók, amelyek bemutatják a folyamat összes lépését az **adott forgatókönyvek** esetében is. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példa útmutatói](walkthroughs.md) című témakörben. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

A következő példákban megtudhatja, hogyan hajthat végre lépéseket a csoportos adatelemzési folyamat lépéseiben Azure Machine Learning Studio (klasszikus) használatával, lásd: az [Azure ml](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) képzési útvonala.

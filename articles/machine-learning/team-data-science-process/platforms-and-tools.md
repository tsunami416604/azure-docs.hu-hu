---
title: Platformok és eszközök adatelemzési projektekhez - Csapatadat-elemzési folyamat
description: Részletezi és ismerteti a vállalati vállalatok számára a team data data science folyamat szabványosítása rendelkezésre álló adatokat és elemzési erőforrásokat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d26dc55cfd0f2ef0bb78e4153fffe3f1cb910b73
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474868"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformok és eszközök adatelemzési projektekhez

A Microsoft az elemzési erőforrások teljes spektrumát biztosítja mind a felhőbeli, mind a helyszíni platformokszámára. Ezek üzembe helyezhetők, hogy az adatelemzési projektek végrehajtása hatékony és méretezhető legyen. Az adatelemzési projekteket nyomon követhető, verzióvezérelt és együttműködésen alapuló projekteket megvalósító csapatokszámára a [Team Data Science Process](overview.md) (TDSP) útmutatást nyújt.  A személyzeti szerepkörök és a hozzájuk tartozó feladatok vázlatát, amelyeket egy adatelemzési csapat kezel, és amely egységesíti ezt a folyamatot, lásd: [Csapatadat-elemzési folyamat szerepkörök és feladatok.](roles-tasks.md)

A TDSP-t használó adatelemzési csoportok számára elérhető elemzési erőforrások a következők:

- Adattudomány virtuális gépek (Windows és Linux CentOS egyaránt)
- HDInsight Spark-fürtök
- Synapse Analytics
- Azure Data Lake
- HDInsight-struktúrafürtök
- Azure File Storage
- SQL Server 2019 R és Python Services
- Azure Databricks

Ebben a dokumentumban röviden ismertetjük az erőforrásokat, és hivatkozásokat adunk meg a TDSP-csapatok által közzétett oktatóanyagokra és forgatókönyvekre. Segítségével megtanulhatja, hogyan használhatja őket lépésről lépésre, és elkezdi használni őket az intelligens alkalmazások létrehozásához. Ezekről az erőforrásokról további információk találhatók a termékoldalaikon. 

## <a name="data-science-virtual-machine-dsvm"></a>Adatelemzési virtuális gép (DSVM)

A Microsoft által Windows és Linux rendszeren is elérhető adatelemzési virtuális gép népszerű eszközöket tartalmaz az adatelemzési modellezéshez és a fejlesztési tevékenységekhez. Ez magában foglalja az eszközöket, mint például:

- Microsoft R Server Developer Edition 
- Anaconda Python-disztribúció
- Jupyter notebookok Python és R 
- Visual Studio Community Edition Python és R eszközökkel Windowsrendszeren / Eclipse Linuxon
- Windows power BI asztal
- SQL Server 2016 Developer Edition Windows rendszeren / Postgres Linuxon

Ez is ml **és AI szerszámok** szeret xgboost, mxnet, és Vowpal Wabbit.

Jelenleg a DSVM **elérhető Windows** és Linux **CentOS** operációs rendszerekben. Válassza ki a DSVM méretét (a CPU-magok számát és a memória mennyiségét) a végrehajtani kívánt adatelemzési projektek igényei alapján. 

A DSVM Windows-kiadásáról további információt a [Microsoft Data Science virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) az Azure Piactéren című témakörben talál. A DSVM Linux-kiadásáról lásd: [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu).

Ha meg szeretné tudni, hogyan hajthatja végre hatékonyan a DSVM-en végzett gyakori adatelemzési feladatokat, tekintse [meg a 10 dolgot, amelyet az adatelemzési virtuális gépen végezhet.](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark-fürtök

Az Apache Spark egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. A Spark feldolgozómotor a sebesség, a könnyű használat és a kifinomult analitika érdekében készült. A Spark memórián belüli számítási képességei jó választássá teszik a gépi tanulás és a gráfszámítások iteratív algoritmusai számára. A Spark is kompatibilis az Azure Blob storage (WASB), így a meglévő adatok az Azure-ban tárolt könnyen feldolgozható a Spark használatával.

Amikor Spark-fürtöt hoz létre a HDInsightban, olyan Azure számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. Körülbelül 10 percet vesz igénybe egy Spark-fürt létrehozása a HDInsightban. Az Azure Blob storage-ban feldolgozandó adatokat tárolja. Az Azure Blob Storage fürttel való használatáról a [HDFS-kompatibilis Azure Blob storage használata a Hadoopsegítségével a HDInsightban című témakörben](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)talál.

A Microsoft TDSP csapata két teljes körű forgatókönyvet tett közzé arról, hogyan használhatja az Azure HDInsight Spark-fürtöket adatelemzési megoldások létrehozásához, az egyiket a Python, a másik a Scala használatával. Az Azure HDInsight **Spark-fürtökről**további információt az [Áttekintés: Apache Spark a HDInsight Linuxon](../../hdinsight/spark/apache-spark-overview.md)című témakörben talál. Ha meg szeretné tudni, hogyan hozhat létre adatelemzési megoldást a **Python** használatával egy Azure HDInsight Spark-fürtön, [olvassa el az Adattudomány áttekintése](spark-overview.md)az Azure HDInsight Spark használatával című témakört. Ha meg szeretné tudni, hogyan hozhat létre adatelemzési megoldást a **Scala** használatával egy Azure HDInsight Spark-fürtön, olvassa el [az Azure-beli Scala és Spark adatelemzési témakört.](scala-walkthrough.md) 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az Azure SQL Data Warehouse lehetővé teszi a számítási erőforrások egyszerű és másodperces méretezését túlterhelés vagy túlfizetés nélkül. Emellett egyedülálló lehetőséget kínál a számítási erőforrások használatának szüneteltetésére, így szabadon kezelheti a felhőköltségeit. A méretezhető számítási erőforrások üzembe helyezésének lehetősége lehetővé teszi, hogy az összes adatot az Azure SQL Data Warehouse-ba vigye. A tárolási költségek minimálisak, és csak az elemezni kívánt adatkészletek részein futtathat számítást. 

Az Azure SQL Data Warehouse szolgáltatásról további információt az [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webhelyén talál. Ha meg szeretné tudni, hogyan hozhat létre teljes körű, fejlett elemzési megoldásokat az SQL Data Warehouse használatával, olvassa el [a Csapat adatelemzési folyamata működés közben: az SQL Data Warehouse használatával című témakört.](sqldw-walkthrough.md)


## <a name="azure-data-lake"></a>Azure Data Lake

Az Azure Data Lake egy vállalati szintű tárházként minden típusú, egyetlen helyen, a formális követelmények vagy a séma előírása előtt gyűjtött adatok nagyvállalati szintű tárháza. Ez a rugalmasság lehetővé teszi, hogy minden típusú adatot egy adattóban tároljon, függetlenül annak méretétől vagy szerkezetétől, vagy attól, hogy milyen gyorsan kerül feldolgozásra. A szervezetek ezután a Hadoop vagy a speciális elemzéssegítségével mintákat kereshetnek ezekben az adattavakban. Az adattavak az alacsonyabb költségű adatok előkészítésének tárházaként is szolgálhatnak, mielőtt az adatokat egy adattárházba helyezne át.

Az Azure Data Lake szolgáltatásról az [Azure Data Lake bemutatása](https://azure.microsoft.com/blog/introducing-azure-data-lake/)című témakörben talál további információt. Ha meg szeretné tudni, hogyan hozhat létre skálázható, végpontok között adatelemzési megoldást az Azure Data Lake-del, olvassa el [a skálázható adattudomány című témakört az Azure Data Lake: Egy végpontok között című témakörben.](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop) fürtök

Az Apache Hive a Hadoop adattárház-rendszere, amely lehetővé teszi az adatok összegzését, lekérdezését és az adatok elemzését a HiveQL használatával, amely az SQL-hez hasonló lekérdezési nyelv. Hive segítségével interaktívan vizsgálja meg az adatokat, vagy hozzon létre újrafelhasználható kötegelt feldolgozási feladatok.

Hive lehetővé teszi, hogy a nagyrészt strukturálatlan adatok struktúráját. Miután definiálta a struktúrát, a Hive segítségével lekérdezheti az adatokat egy Hadoop-fürtben anélkül, hogy Java vagy MapReduce használatát kellene használnia, vagy akár ismernie kellene. HiveQL (a Hive lekérdezési nyelv) lehetővé teszi, hogy a T-SQL-hez hasonló utasításokkal lekérdezéseket írjon.

Adatszakértők számára a Hive futtathatja a Python-felhasználó által definiált függvények (UDF-ek) a Hive-lekérdezések rekordok feldolgozásához. Ez a képesség jelentősen kiterjeszti a Hive-lekérdezések képességét az adatelemzésben. Pontosabban lehetővé teszi az adatszakértők számára, hogy skálázható szolgáltatástervezést végezzenek olyan nyelveken, amelyeket többnyire ismernek: az SQL-szerű HiveQL és python. 

Az Azure HDInsight-struktúrafürtökről a [Hive és a HiveQL használata a Hadoophasználatával a HDInsightban című témakörben talál](../../hdinsight/hadoop/hdinsight-use-hive.md)további információt. Ha meg szeretné tudni, hogyan hozhat létre skálázható teljes körű adatelemzési megoldást az Azure HDInsight-fürtös fürtökkel, olvassa el [a Team Data Science Process in action: HDInsight Hadoop-fürtök használata című témakört.](hive-walkthrough.md)


## <a name="azure-file-storage"></a>Azure File Storage 

Az Azure File Storage egy olyan szolgáltatás, amely a szabványos Server Message Block (SMB) protokoll használatával kínál fájlmegosztásokat a felhőben. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. Az Azure virtuális gépeken vagy felhőszolgáltatásában, esetleg helyszíni ügyfeleken üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Különösen hasznos adatelemzési projektek az a képesség, hogy hozzon létre egy Azure fájltároló, mint a hely, ahol a projekt adatok megosztása a projekt csapat tagjai. Mindegyikük ezután hozzáférhet az adatok ugyanazon példányát az Azure-fájltárolóban. Ezt a fájltárolót a projekt végrehajtása során létrehozott szolgáltatáskészletek megosztására is használhatják. Ha a projekt ügyfélelköteleződés, az ügyfelek létrehozhatnak egy Azure-fájltárhelyet a saját Azure-előfizetésük alatt, hogy megosszák Önnel a projektadatokat és -funkciókat. Ily módon az ügyfél teljes hozzáféréssel rendelkezik a projekt adateszközeifelett. Az Azure File Storage szolgáltatásról további információt az [Azure File storage windowsos használatának és](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) az Azure File Storage Linux használatával című [témakörben](../../storage/files/storage-how-to-use-files-linux.md)talál.


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R és Python Services

Az R-szolgáltatások (adatbázison belüli) platformot biztosít az intelligens alkalmazások fejlesztéséhez és üzembe helyezéséhez, amelyek új elemzéseket tárhatnak fel. A gazdag és hatékony R-nyelv, beleértve az R közösség által biztosított számos csomagot is használhatja modellek létrehozásához és az SQL Server-adatokból származó előrejelzések létrehozásához. Mivel az R-szolgáltatások (adatbázison belüli) integrálja az R nyelvet az SQL Server rel, az elemzések az adatok közelében maradnak, ami kiküszöböli az adatok áthelyezésével kapcsolatos költségeket és biztonsági kockázatokat.

Az R Services (In-database) az SQL Server eszközök és technológiák átfogó készletével támogatja a nyílt forráskódú R nyelvet. Kiváló teljesítményt, biztonságot, megbízhatóságot és kezelhetőséget kínálnak. Az R-megoldásokat kényelmes és ismerős eszközökkel telepítheti. Az éles alkalmazások hívhatják az R-futásidejű, és beolvasni előrejelzések és vizualizációk transact-SQL használatával. A ScaleR-kódtárak segítségével is javíthatja az R-megoldások méretezését és teljesítményét. További információt az [SQL Server R Services című témakörben talál.](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)

A Microsoft TDSP csapata két teljes körű forgatókönyvet tett közzé, amelyek bemutatják, hogyan hozhat létre adatelemzési megoldásokat az SQL Server 2016 R Services szolgáltatásban: egyet az R programozók, egyet pedig az SQL-fejlesztők számára. Az **R-programozók**esetében lásd: [Adatelemzési végpontok között forgatókönyv.](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough) **Az SQL Developers for** [In-Database Advanced Analytics for SQL Developers (Oktatóanyag) című](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)témakörben található.


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Függelék: Adatelemzési projektek beállításának eszközei

### <a name="install-git-credential-manager-on-windows"></a>A Git hitelesítő adatok kezelőjének telepítése Windows rendszeren

Ha a TDSP-t követi **a Windows**rendszeren, telepítenie kell a **Git Credential Managert (GCM)** a Git-tárházakkal való kommunikációhoz. A GCM telepítéséhez először telepítenie kell a **Chocolaty-t**. A Chocolaty és a GCM telepítéséhez futtassa **rendszergazdaként**a következő parancsokat a Windows PowerShellben:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>A Git telepítése Linux (CentOS) gépekre

A Git Linux (CentOS) gépekre való telepítéséhez futtassa a következő bash parancsot:

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Nyilvános SSH-kulcs létrehozása Linux (CentOS) gépeken

Ha Linux (CentOS) gépeket használ a git-parancsok futtatásához, hozzá kell adnia a számítógép nyilvános SSH-kulcsát az Azure DevOps-szolgáltatásokhoz, hogy ezt a gépet az Azure DevOps-szolgáltatások felismerjék. Először létre kell hoznia egy nyilvános SSH-kulcsot, és hozzá kell adnia a kulcsot az SSH nyilvános kulcsaihoz az Azure DevOps Services biztonsági beállítási lapján. 

1. Az SSH-kulcs létrehozásához futtassa a következő két parancsot: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Az SSH kulcs létrehozásának parancsai](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Másolja a teljes ssh gombot, beleértve *az ssh-rsa parancsot*is. 
1. Jelentkezzen be az Azure DevOps-szolgáltatásokba. 
1. Kattintson **<\> a lap** jobb felső sarkában, majd kattintson a **biztonság**gombra. 
    
   ![Kattintson a nevére, majd a biztonságra](./media/platforms-and-tools/resources-2-user-setting.png)

1. Kattintson **az SSH nyilvános kulcsok**ikonra, majd a **+Hozzáadás**gombra. 

   ![Kattintson az SSH nyilvános kulcsok, majd a +Hozzáadás](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Illessze be a szövegmezőbe másolt ssh billentyűt, és mentse.


## <a name="next-steps"></a>További lépések

Teljes körű forgatókönyvek, amelyek bemutatják a folyamat összes lépését az **adott forgatókönyvek** is rendelkezésre állnak. Ezek a példa forgatókönyvek témakörben vannak felsorolva és összekapcsolva a [miniatűrleírásokkal.](walkthroughs.md) Bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatba vagy folyamatba egy intelligens alkalmazás létrehozásához. 

Példák, amelyek bemutatják, hogyan hajtsa végre a lépéseket a team data science folyamat segítségével Azure Machine Learning Studio (klasszikus), tekintse meg az [Azure ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) tanulási útvonal.

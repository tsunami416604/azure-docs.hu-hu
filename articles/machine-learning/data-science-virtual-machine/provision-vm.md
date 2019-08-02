---
title: A Windows Data Science virtuális gép létrehozása
titleSuffix: Azure
description: Konfigurálja és a egy adatelemző virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: dfb3e9f6390d4c80b8f3c37b87f2659c671fa823
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591823"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Windows Data Science Virtual Machine kiépítése az Azure-ban

A Microsoft Windows Data Science Virtual Machine (DSVM) egy Azure-beli Windows Server 2016 rendszerű virtuális gép (VM), amely az adatok elemzéséhez és a gépi tanuláshoz szükséges eszközökkel lett előtelepítve és konfigurálva.

## <a name="included-data-science-tools"></a>Tartalmazott adatelemzési eszközöket

A DSVM a következő eszközöket tartalmazza:

* [Azure Machine learning szolgáltatás](../service/index.yml) Python SDK
* [Microsoft Machine learning Server](https://docs.microsoft.com/machine-learning-server/index) Fejlesztői kiadás
* Anaconda Python elosztási
* Jupyter Notebook R-, Python-és PySpark-kernelekkel
* Microsoft Visual Studio Community
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 fejlesztői kiadás
* Önálló Apache Spark-példány helyi fejlesztéshez és teszteléshez
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning és az adatok elemzési eszközök:
  * Deep learning-keretrendszerek – az AI-keretrendszerek széles választékát tartalmazza a virtuális gép: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [láncolás](https://chainer.org/), mxNet és kerasz
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – egy gyors gépi tanulási rendszer, amely olyan technikákat támogat, mint például az online kivonatolás, a allreduce, a csökkenés, a learning2search, valamint az aktív és interaktív tanulás
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) – egy olyan eszköz, amely gyors és pontos, lendületes faszerkezetes megvalósítást biztosít
  * [Csörgő](https://togaware.com/rattle/) – az r analitikai eszköz, amely az r-ben megkezdi az adatelemzést és a gépi tanulást. Ez magában foglalja a GUI-alapú adatelemzést és-modellezést automatikus R-kód generálásával.
  * [WEKA](https://www.cs.waikato.ac.nz/ml/weka/) – vizuális adatbányászati és gépi tanulási szoftver Java-ban
  * [Apache Drill](https://drill.apache.org/) – egy séma nélküli SQL-lekérdezési motor Apache Hadoop-, NoSQL-és felhőalapú tároláshoz. A nosql-alapú, és a standard szintű BI eszközök, Power BI, a Microsoft Excel és a Tableau fájlok ODBC, JDBC és felületek támogatja.
* Az R és Python for kódtárak használata az Azure Machine Learning és más Azure-szolgáltatások
* A Git, beleértve a Git bash eszközt, például a GitHub és az Azure DevOps forráskódtárházak dolgozhat. A Git nyújt számos népszerű Linux parancssor eszközök, amelyek elérhetők mind a Git Bash és a egy parancssort. Példák awk, a csökkentésének, a perl, a grep, a keresés, a wget és a curl.

### <a name="about-data-science"></a>Tudnivalók az adattudományról

Az adatelemzés magában foglalja a feladatok egy sorozatát léptetés:

1. Az adatkeresés, betöltés és előfeldolgozás
1. Modellek létrehozása és tesztelése
1. Modellek üzembe helyezése intelligens alkalmazásokban

Az adatszakértők a feladatokhoz számos eszközt használja. Kereshet a szoftver megfelelő verzióját, majd töltse le és telepítse őket a időigényes lehet. A DSVM időt takaríthat meg egy használatra kész, az Azure-ban üzembe helyezhető, előre telepített és konfigurált népszerű eszköz használatával.

A DSVM Jump-elindítja az elemzési projektet. A feladatok különféle nyelveken, köztük a R, Python, SQL és C# dolgozhat. A Visual Studio fejlesztése és tesztelése a kód egyszerűen használható integrált fejlesztési környezetben (IDE) biztosít. Az Azure SDK-t a virtuális gép tartalmazza, így a Microsoft felhőalapú platformján számos szolgáltatás segítségével hozhatja létre alkalmazásait.

Nincsenek szoftvereket díjmentesen használható a data science Virtuálisgép-rendszerképet. Csak az Azure-használati díjat kell fizetnie. A virtuális gép üzembe helyezésekor méretétől függnek. További részleteket a számítási díjak a **díjszabás** szakaszában a [adatelemző virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) lapot.

### <a name="other-dsvm-versions"></a>Egyéb DSVM-verziók

* Egy [Ubuntu](dsvm-ubuntu-intro.md) kép. A dsvm-hez hasonló számos eszközt, valamint néhány további mélytanulási keretrendszerekkel rendelkezik.
* A [Linux CentOS](linux-dsvm-intro.md) kép.
* A [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) , az adatelemzési virtuális gépet. Néhány eszközök csak a Windows Server 2016 rendszeren érhetők el. Ez a cikk ellenkező esetben is érvényes a Windows Server 2012 verzióra.

## <a name="prerequisite"></a>Előfeltétel

A Microsoft adatelemző virtuális gép létrehozásához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.com/free).

## <a name="create-your-dsvm"></a>A DSVM létrehozása

DSVM-példány létrehozása:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)virtuális gép listáját. Előfordulhat, hogy a rendszer felszólítja, hogy jelentkezzen be az Azure-fiókjába, ha még nincs bejelentkezve.
1. Válassza a **Létrehozás** gombot alul.

   ![Konfigurálja-data-adatelemzési – a virtuális gép](./media/provision-vm/configure-data-science-virtual-machine.png)

1. A képernyőkép jobb oldali ablaktábláján megjelenő lépések konfigurálásához a következő információkat kell megadnia:

   1. **Alapvető beállítások**:
      * **Name (név**): a DSVM neve
      * **VM-lemez típusa**: vagy **SSD** vagy **HDD**. Egy hasonló NVidia Tesla K80-alapú NC_v1 GPU-példány, válassza ki a **HDD** , a lemez típusát.
      * **Felhasználónév**: a rendszergazdai fiók azonosítója
      * **Password (jelszó**): a rendszergazdai fiók jelszava
      * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.
      * **Erőforráscsoport**. Létrehozhat egy új vagy egy meglévő csoportot.
      * **Hely**. Válassza ki az Adatközpont leginkább megfelelő. A leggyorsabb hálózati hozzáféréshez az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a fizikai helyéhez legközelebbi.
   1. **Méret**: Válassza ki a kiszolgáló típusát, amely megfelel a funkcionális követelmények és költségek megkötések. További lehetőségek a Virtuálisgép-méretek, válasszon **nézet összes**.
   1. **Beállítások**:  
      * **Felügyelt lemezek használata**. Válasszon **felügyelt** Ha azt szeretné, hogy Azure-lemezek kezelése a virtuális gép számára. Ha nem, akkor meg kell adnia egy új vagy meglévő tárfiókot.  
      * **Más paramétereket**. Az alapértelmezett értékeket is használhat. Ha nem alapértelmezett értékeket szeretne használni, vigye az egérmutatót az információs hivatkozás fölé az adott mezők súgójában.
   1. **Összefoglalás**: Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> * A virtuális gép nem számít fel további díjat a **méret** lépésben kiválasztott kiszolgáló méretére vonatkozó számítási költségeken túl.
> * Üzembe helyezési idő körülbelül 10 és 20 perc szükséges. A virtuális gép állapotát a Azure Portal tekintheti meg.

## <a name="how-to-access-the-dsvm"></a>A DSVM elérése

Miután a virtuális gép létrehozása és üzembe helyezett, segítségével a távoli asztal bele a rendszergazdai fiók hitelesítő adatait, amely az előző konfigurált **alapjai** szakaszban. Készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. A Start menü csempéi és asztali ikonjai számos eszközt is elérhet.

Data Science VM is csatolhat, hogy Azure Notebooks Jupyter jegyzetfüzeteket futtasson a virtuális gépen, és megkerüli az ingyenes szolgáltatási szintet. További információ: jegyzetfüzetek [projektjeinek kezelése és konfigurálása – számítási réteg](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gépen telepített eszközök

További információ a DSVM telepített eszközökről:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Használhatja a Microsoft Enterprise Library méretezhető R vagy Python elemzési, mert a Machine Learning Server Developer edition telepítve van a virtuális gépen. Korábbi nevén Microsoft R Server, Machine Learning Server egy széles körben telepíthető, nagyvállalati szintű elemzési platform. Az R és a Python esetében egyaránt elérhető. Emellett méretezhető, kereskedelmileg támogatott és biztonságos.

Machine Learning-kiszolgáló támogatja a különböző big data típusú statisztikai, prediktív modellezési és gépi tanulási feladatok. Az elemzések széles skálását támogatja: feltárás, elemzés, vizualizációs és modellezés. Szerint használ, és kiterjeszti a nyílt forráskódú R és Python, a Machine Learning-kiszolgáló a kompatibilis az R és Python-parancsfájlokban és függvényekben. Emellett akkor is kompatibilis a CRAN, a pipet és a Conda-csomagok az adatok a nagyvállalati szintű elemzésére.

Machine Learning-kiszolgáló hozzáadásával a párhuzamos és darabolásos adatok feldolgozása a memórián belüli korlátozások, nyílt forráskódú R címek. Ez azt jelenti, hogy az elemzést sokkal nagyobb adatmennyiségen futtathatja, mint a fő memóriában. A Visual Studio Community tartalmazza a virtuális gépen. A Visual Studio és a Python Tools for Visual Studio (PTVS) olyan R Tools-bővítményekkel rendelkezik, amelyek teljes körű IDE-t biztosítanak az R vagy a Python használatához. Is biztosítanak, mint más IDEs [RStudio](https://www.rstudio.com) és [PyCharm Community edition](https://www.jetbrains.com/pycharm/) a virtuális gépen.

### <a name="python"></a>Python

A Python használatával történő fejlesztéshez a 2,7-es és a 3,6-es anaconda Python-disztribúciók vannak telepítve. Ezek a disztribúciók körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python rendelkezik. Projektek készítése PTVS, amelyre telepítve van a Visual Studio Community 2017 is használhatja. Vagy az Ide-k, például TÉTLEN vagy Spyder Anaconda mellékelhető egyikét használhatja. Keresse meg és indítsa el ezeket a csomagokat (Win + S) egyikét.

> [!NOTE]
> Mutasson a Python Tools for Visual Studio, az Anaconda Python 2.7-es, szeretne létrehozni az egyéni környezet minden verzióhoz. E környezet elérési útvonalak beállítása a Visual Studio 2017 Community, navigáljon a **eszközök** > **Python Tools** > **Python-környezetek**. Válassza ki **+ egyéni**.

Anaconda Python 3.6-os verziója telepítve van a **C:\Anaconda**. Anaconda Python 2.7-es telepítőmappájában **c:\Anaconda\envs\python2**. Részletes lépéseiért lásd: [PVTS dokumentációban](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>A Jupyter Notebook

A Jupyter Notebookot, megosztani a kódot és az elemzési környezet anaconda terjesztési is tartalmaz. A Jupyter Notebook kiszolgáló előre konfigurálva vannak a Python 2.7-es, Python 3.x, PySpark, Julia és R kernelekkel. A Jupyter-kiszolgáló elindításához és a böngésző megnyitásához a notebook-kiszolgáló eléréséhez használja a **Jupyter notebook** asztal ikonját.

Mi a Python és az r segítségével számos mintafüzetek csomag Jupyter éri el, miután a notebookok megjelenítése a következő technológiákat használata:

* Machine Learning Server
* SQL Server Machine Learning Services, adatbázis-elemzés
* Python
* Microsoft kognitív eszközkészlet
* Tensorflow
* Egyéb Azure-technológiák

A mintákra mutató hivatkozás a jegyzetfüzet kezdőlapján található, miután a Jupyter Notebook hitelesítését a korábbi lépésben létrehozott jelszó használatával végezheti el.

### <a name="visual-studio-community-2017"></a>A Visual Studio Community 2017.

A DSVM tartalmazza a Visual Studio Community-t. Ez a Microsoft népszerű IDE-verziójának ingyenes verziója, amelyet kiértékelési célokra és kis csapatoknak is használhatnak. Tekintse meg a [licencfeltételek](https://www.visualstudio.com/support/legal/mt171547).

Nyissa meg a Visual studiót az asztal ikon vagy a **Start** menü használatával. Keresse meg a programok (Win + S), majd a **Visual Studio**. Itt például C#, Python, R és node.js nyelven projekteket hozhat létre. Telepített modulok könnyítse meg az alábbi Azure-szolgáltatások működéséhez:

* Azure Data Catalog
* Az Azure HDInsight Hadoop és Spark
* Azure Data Lake

A **Visual Studio Code** -hoz Azure Machine learning nevű beépülő modul is működik, amely zökkenőmentesen integrálható a Azure Machine learning, és segít az AI-alkalmazások gyors kiépítésében.

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet, hogy a próbaidőszak lejárt. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új, hozzáférhet a Visual Studio Community ingyenes fiókot.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

A DSVM a SQL Server 2017 fejlesztői verziójával jön Machine Learning Services. Ez a SQL Server-kiadás R vagy Python nyelven érhető el, és képes az adatbázis-elemzések futtatására. Machine Learning-szolgáltatások fejlesztéséhez és üzembe helyezéséhez intelligens alkalmazások platformot biztosít. Ezeket a nyelveket és a Közösségtől számos csomagok segítségével modelleket hozhat létre és előrejelzések készítése az SQL Server-adatok. Elemzési adatokhoz közel képes tartani, mivel a Machine Learning-szolgáltatások, az adatbázis-, integrálható az SQL Server-kiszolgálón belül az R- és Python nyelveket. Ez az integráció kiküszöböli a költségek és az adatáthelyezés biztonsági kockázatokat.

> [!NOTE]
> Az SQL Server Developer edition csak fejlesztési és tesztelési célokat szolgál. Éles környezetben futtatásához licenc szükséges.

Az SQL Server a Microsoft SQL Server Management Studio elindításával érheti el. A virtuális gép neve fel van töltve, mint a **kiszolgálónév**. Windows-hitelesítést használ a jelentkezik be, a rendszergazda a Windows. Amikor az SQL Server Management Studióban, más felhasználók létrehozása, adatbázisok létrehozása, adatok importálása, és futtasson SQL-lekérdezéseket.

Engedélyezése az adatbázison belüli elemzések SQL Machine Learning-szolgáltatások használatával, akkor futtassa a következő parancsot, az SQL Server Management Studióban egy egyszeri művelet, miután kiszolgálói rendszergazdaként jelentkezzen be:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Cserélje `%COMPUTERNAME%` le a nevet a virtuális gép nevére.

### <a name="azure"></a>Azure

Több Azure-eszközök telepítve vannak a virtuális gépen:

* Asztali parancsikon kerül az Azure SDK dokumentációját.
* Az **AzCopy** használatával másolhatja az Azure Storage-fiókját és onnan kívüli adatait. Használati megtekintéséhez írja be a **Azcopy** parancsot a parancssorba.
* Használat **Azure Storage Explorer** , keresse meg az objektumokat az Azure Storage-fiókban tárolja. Emellett az Azure Storage-ba irányuló és onnan másolt adatok is. Az eszköz eléréséhez írja be a **Storage Explorer** **kifejezést a keresőmezőbe** . Vagy keresse meg a Windows **Start** menüjében.
* A **Adlcopy** átmásolja az Azure Data Lakeba. Használati megtekintéséhez írja be a **adlcopy** parancsot.
* a **dtui** átmásolja a NoSQL-adatbázist a felhőbe, és onnan másolja át az adatait Azure Cosmos DBba. Adja meg **dtui** parancsot.
* **Azure Data Factory Integration Runtime** a helyszíni adatforrások és a felhő közötti Adatmásolást. Eszközök, mint például az Azure Data Factory belül használható.
* A **Microsoft Azure PowerShell** használatával felügyelheti Azure-erőforrásait a PowerShell programozási nyelvén. Azt is telepítve van a virtuális Gépen.

### <a name="power-bi"></a>Power BI

A DSVM az irányítópultok és a vizualizációk létrehozásához a **Power bi Desktop** telepítésével jön létre. Az eszköz használható az adatok különböző forrásokból származó az irányítópultokat és jelentéseket készíthet, és közzéteheti őket a felhőbe. További információkért lásd: a [Power BI](https://powerbi.microsoft.com) hely. Power BI Desktop a **Start** menüben található.

> [!NOTE]
> Egy Power BI eléréséhez a Microsoft Office 365-fiók szükséges.

### <a name="azure-machine-learning-service-python-sdk"></a>Az Azure Machine Learning szolgáltatás Python SDK-t

Az adatszakértők és a mesterséges intelligencia-fejlesztők a Azure Machine Learning SDK for Pythont használják a gépi tanulási munkafolyamatok létrehozásához és futtatásához a [Azure Machine learning szolgáltatással](../service/overview-what-is-azure-ml.md). A szolgáltatást Jupyter jegyzetfüzetekben vagy más Python IDE-ben is használhatja nyílt forráskódú keretrendszerek, például a TensorFlow és a scikit-Learn használatával.

A Python SDK használatának első lépései, lásd: [az Azure Machine Learning használatának első lépései a Python](../service/quickstart-create-workspace-with-python.md).

A Python SDK telepítve van a Microsoft Data Science virtuális gépen.

## <a name="more-microsoft-development-tools"></a>További Microsoft fejlesztési eszközök

A Microsoft webplatform [](https://www.microsoft.com/web/downloads/platform.aspx) -telepítővel más Microsoft fejlesztői eszközöket is megtalálhat és tölthet le. Az eszközre a Microsoft Data Science Virtual Machine asztalán is található parancsikon.  

## <a name="important-directories-on-the-vm"></a>A virtuális gép fontos könyvtárak

| Elem | Címtár |
| --- | --- |
| Jupyter Notebook server konfigurációk | C:\ProgramData\jupyter |
| Jupyter Notebook minták kezdőkönyvtár | C:\dsvm\notebooks és c:\Users\\< Felhasználónév\>\notebooks |
| Más minták | C:\dsvm\samples |
| Anaconda, alapértelmezett: Python 3,6 | C:\Anaconda |
| Anaconda Python 2.7-es környezetben | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning-kiszolgálója (önálló) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Az R alapértelmezett példány, Machine Learning-kiszolgáló (önálló) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Machine Learning-szolgáltatások az SQL-adatbázis-példány könyvtár | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Egyéb eszközök | C:\dsvm\tools |

> [!NOTE]
> A dsvm-hez és Windows Server 2016 edition 2018 március előtti Windows Server 2012 kiadásán alapértelmezett Anaconda környezete Python 2.7-t. A másodlagos környezet a Python 3,5, amely a következő helyen található: **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>További lépések

* Fedezze fel az eszközök az adatelemző virtuális gép kiválasztásával a **Start** menü.
* Ismerje meg az Azure Machine Learning szolgáltatással kapcsolatban, olvassa el [Mi az Azure Machine Learning szolgáltatás?](../service/overview-what-is-azure-ml.md) , illetve próbálhatja ki a [útmutatóink és oktatóanyagaink segítségével](../service/index.yml) elérhető.
* A Fájlkezelőben navigáljon a **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** for Samples elemre, amely az R RevoScaleR-könyvtárát használja, amely támogatja a nagyvállalati szintű adatelemzést.  
* A cikk a [tíz dolog, amire alkalmas az adatelemző virtuális gép](https://aka.ms/dsvmtenthings).
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml).
* Látogasson el a [Azure AI-katalógusban](https://gallery.cortanaintelligence.com) machine learning és a data analytics minták az Azure Machine Learning és a kapcsolódó adatokat használó szolgáltatások az Azure-ban. Is biztosítunk egy ikont a tárban lévő a **Start** menüjében és asztalán a virtuális gép.

---
title: A Windows Data Science virtuális gép Azure-beli üzembe helyezése |} A Microsoft Docs
description: Konfigurálja és a egy adatelemző virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: gokuma
ms.openlocfilehash: 1b293ee8f0f83d727cd647cdcdcc424b4db7e5d3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240885"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>A Windows Data Science virtuális gép Azure-beli üzembe helyezése
A Microsoft Data Science virtuális gépet (DSVM) egy Windows Azure virtuális gép (VM) rendszerkép. Előtelepített és konfigurált adatelemzési és machine learning használt számos eszközzel. A következő eszközök foglalja magában:

* [Az Azure Machine Learning](../service/index.yml) Workbench.
* [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/machine-learning-server/index) Developer Edition szoftverhez.
* Anaconda Python elosztási.
* Jupyter Notebook az R, Python és a PySpark kernelt.
* A Microsoft Visual Studio Community.
* A Microsoft Power BI desktopban.
* A Microsoft SQL Server 2017 Developer edition.
* Egy önálló Apache Spark-példányt helyi fejlesztési és tesztelési célra.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning és az adatok elemzési eszközök:
  * Deep learning-keretrendszerek. AI-keretrendszerek gazdag választékát részét képezik a virtuális gép: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, és Keras.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit). Gyors machine learning-rendszer, amely támogatja az online kivonatoláshoz, allreduce, csökkentésének, learning2search és aktív és interaktív tanulási eljárások.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/). Egy eszköz, gyorsított fa gyors és pontos végrehajtását.
  * [Rattle](https://togaware.com/rattle/), az R analitikai eszközt, hogy könnyedén megtudhatja. Olyan eszköz, amely megkönnyíti az adatok elemzési és gépi tanulási az r használatába Ez magában foglalja a GUI-alapú adatáttekintés és modellezés az R-kód automatikus generálása.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/). Vizuális adatbányászat és a gépi tanulási Java szoftvert.
  * [Apache Drill](https://drill.apache.org/). Séma nélküli SQL lekérdezési motorja Apache Hadoop, a nosql-alapú és a felhőalapú tárolást.  A nosql-alapú, és a standard szintű BI eszközök, Power BI, a Microsoft Excel és a Tableau fájlok ODBC, JDBC és felületek támogatja.
* Kódtárak R és Python esetében az Azure Machine Learning és más Azure-szolgáltatásokat használja.
* A Git, beleértve a Git bash eszközt, például a GitHub és az Azure DevOps forráskódtárházak dolgozhat. A Git nyújt számos népszerű Linux parancssor eszközök, amelyek elérhetők mind a Git Bash és a egy parancssort. Példák awk, a csökkentésének, a perl, a grep, a keresés, a wget és a curl.

Az adatelemzés magában foglalja a feladatok egy sorozatát léptetés:

1. Keresse meg, betöltését és az adatok előfeldolgozása.
1. Hozhat létre, és tesztelni tudtuk.
1. A modellek intelligens alkalmazásokban felhasználásra üzembe helyezése.

Az adatszakértők a feladatokhoz számos eszközt használja. Kereshet a szoftver megfelelő verzióját, majd töltse le és telepítse őket a időigényes lehet. A Microsoft Data Science virtuális gép időt takaríthat meg azáltal, hogy egy használatra kész-rendszerképet, amelyek kioszthatóak az Azure-ban előtelepített és konfigurált számos népszerű eszközzel. 

A Microsoft Data Science virtuális gép jump-starts az elemzési projekt. A feladatok különféle nyelveken, köztük a R, Python, SQL és C# dolgozhat. A Visual Studio fejlesztése és tesztelése a kód egyszerűen használható integrált fejlesztési környezetben (IDE) biztosít. Az Azure SDK-t a virtuális gép szerepel. Így hozhat létre az alkalmazások a Microsoft felhőalapú platformja különböző szolgáltatások használatával. 

Nincsenek szoftvereket díjmentesen használható a data science Virtuálisgép-rendszerképet. Csak az Azure-használati díjat kell fizetnie. A virtuális gép üzembe helyezésekor méretétől függnek. További részleteket a számítási díjak a **díjszabás** szakaszában a [adatelemző virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) lapot. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Egyéb verziói, a Data Science virtuális gép
* Egy [Ubuntu](dsvm-ubuntu-intro.md) kép. A dsvm-hez hasonló számos eszközt, valamint néhány további mélytanulási keretrendszerekkel rendelkezik. 
* A [Linux CentOS](linux-dsvm-intro.md) kép.
* A [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) , az adatelemzési virtuális gépet. Néhány eszközök csak a Windows Server 2016 rendszeren érhetők el. Ez a cikk ellenkező esetben is érvényes a Windows Server 2012 verzióra.

## <a name="prerequisite"></a>Előfeltétel
A Microsoft adatelemző virtuális gép létrehozásához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gép létrehozása
Hozzon létre egy példányt, a Microsoft Data Science virtuális gép, kövesse az alábbi lépéseket:

1. Keresse meg a virtuális gépet, az ajánlati a [az Azure portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). A rendszer felkérheti az Azure-fiókkal bejelentkezni, ha már nincs bejelentkezve.
1. Válassza ki a **létrehozás** gombra az alsó kell figyelembe venni a varázslót.

  ![Konfigurálja-data-adatelemzési – a virtuális gép](./media/provision-vm/configure-data-science-virtual-machine.png) 

1. A varázsló, amely létrehozza a Microsoft Data Science virtuális gép szükséges **bemeneti**. A következő bemenet konfigurálása jobb oldalán található ábrán bemutatott lépések mindegyike van szükség:

  a. **Alapvető beállítások**:

    i. **Név**. Neve az adatelemzési kiszolgálót hoz létre.  

    II. **Virtuális merevlemez típusa**. Válasszon **SSD** vagy **HDD**. Egy hasonló NVidia Tesla K80-alapú NC_v1 GPU-példány, válassza ki a **HDD** , a lemez típusát.   

    III. **Felhasználónév**. Jelentkezzen be a rendszergazdai fiók azonosítója.   

    IV. **Jelszó**. A rendszergazdai fiók jelszavát.  

    v. **Előfizetés**. Ha egynél több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.   

    VI. **Erőforráscsoport**. Létrehozhat egy új vagy egy meglévő csoportot.   

    VII. **Hely**. Válassza ki az Adatközpont leginkább megfelelő. A leggyorsabb hálózati hozzáféréshez az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a fizikai helyéhez legközelebbi.   

  b. **Méret**. Válassza ki a kiszolgáló típusát, amely megfelel a funkcionális követelmények és költségek megkötések. További lehetőségek a Virtuálisgép-méretek, válasszon **nézet összes**.  

  c. **Beállítások**:  

    i. **Felügyelt lemezek használata**. Válasszon **felügyelt** Ha azt szeretné, hogy Azure-lemezek kezelése a virtuális gép számára. Ha nem, akkor meg kell adnia egy új vagy meglévő tárfiókot.  

    II. **Más paramétereket**. Az alapértelmezett értékeket is használhat. Ha azt szeretné, nem alapértelmezett értékek, a kurzort az egyes mezőkkel tájékoztató mutató hivatkozás.  

  d. **Összefoglalás**. Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt. Kattintson a **Létrehozás** gombra. 

> [!NOTE]
> * A virtuális gép nem rendelkezik a további díjakat a számítási költség a kiválasztott kiszolgáló mérete meghaladja a **mérete** . lépés. 
> * Üzembe helyezési idő körülbelül 10 és 20 perc szükséges. Annak állapotát jeleníti meg az Azure Portalon.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gép elérése
Miután a virtuális gép létrehozása és üzembe helyezett, segítségével a távoli asztal bele a rendszergazdai fiók hitelesítő adatait, amely az előző konfigurált **alapjai** szakaszban. Készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. Start menü csempék és asztali ikonok számos olyan eszközzel rendelkezik. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gépen telepített eszközök

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition
Használhatja a Microsoft Enterprise Library méretezhető R vagy Python elemzési, mert a Machine Learning Server Developer edition telepítve van a virtuális gépen. Korábbi nevén Microsoft R Server, a Machine Learning-kiszolgáló egy nagyjából üzembe helyezhető, nagyvállalati szintű elemzési platform. R és Python is elérhető és méretezhető, üzleti szempontból támogatott és biztonságos. 

Machine Learning-kiszolgáló támogatja a különböző big data típusú statisztikai, prediktív modellezési és gépi tanulási feladatok. Az elemzések széles skálását támogatja: feltárás, elemzés, vizualizációs és modellezés. Szerint használ, és kiterjeszti a nyílt forráskódú R és Python, a Machine Learning-kiszolgáló a kompatibilis az R és Python-parancsfájlokban és függvényekben. Emellett akkor is kompatibilis a CRAN, a pipet és a Conda-csomagok az adatok a nagyvállalati szintű elemzésére. 

Machine Learning-kiszolgáló hozzáadásával a párhuzamos és darabolásos adatok feldolgozása a memórián belüli korlátozások, nyílt forráskódú R címek. Ezért analytics futtathatja a fő memóriában adatfeldolgozással-nál nagyobb adatmennyiséget. A Visual Studio Community tartalmazza a virtuális gépen. Az R tools for Visual Studio és a Python Tools, adjon meg egy teljes IDE-R vagy Python használata a Visual Studio (PTVS)-bővítmény rajta. Is biztosítanak, mint más IDEs [RStudio](http://www.rstudio.com) és [PyCharm Community edition](https://www.jetbrains.com/pycharm/) a virtuális gépen. 

### <a name="python"></a>Python
-Fejlesztéshez a Python 2.7-es és 3.6-os Anaconda Python disztribúciók nincsenek telepítve. Ezek a disztribúciók körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python rendelkezik. Projektek készítése PTVS, amelyre telepítve van a Visual Studio Community 2017 is használhatja. Vagy az Ide-k, például TÉTLEN vagy Spyder Anaconda mellékelhető egyikét használhatja. Keresse meg és indítsa el ezeket a csomagokat (Win + S) egyikét.

> [!NOTE]
> Mutasson a Python Tools for Visual Studio, az Anaconda Python 2.7-es, szeretne létrehozni az egyéni környezet minden verzióhoz. E környezet elérési útvonalak beállítása a Visual Studio 2017 Community, navigáljon a **eszközök** > **Python Tools** > **Python-környezetek**. Válassza ki **+ egyéni**. 
> 
> 

Anaconda Python 3.6-os verziója telepítve van a **C:\Anaconda**. Anaconda Python 2.7-es telepítőmappájában **c:\Anaconda\envs\python2**. Részletes lépéseiért lásd: [PVTS dokumentációban](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters). 

### <a name="the-jupyter-notebook"></a>A Jupyter Notebook
A Jupyter Notebookot, megosztani a kódot és az elemzési környezet anaconda terjesztési is tartalmaz. A Jupyter Notebook kiszolgáló előre konfigurálva vannak a Python 2.7-es, Python 3.x, PySpark, Julia és R kernelekkel. Indítsa el a Jupyter-kiszolgálót, és indítsa el a böngészőt a notebook kiszolgálón van egy asztali ikon nevezzük **Jupyter Notebook**. 

Mi a Python és az r segítségével számos mintafüzetek csomag Jupyter éri el, miután a notebookok megjelenítése a következő technológiákat használata:

* Machine Learning-kiszolgáló.
* Az SQL Server Machine Learning-szolgáltatások, adatbázison belüli elemzések. 
* Python.
* A Microsoft Cognitive ToolKit.
* Tensorflow-hoz.
* Más Azure-technológiákat. 

A minták a hivatkozást a jegyzetfüzet kezdőlap után jelenik a korábbi lépésben létrehozott jelszó használatával hitelesítést a Jupyter Notebookot. 

### <a name="visual-studio-community-2017"></a>A Visual Studio Community 2017.
A Visual Studio Community telepítve van a virtuális gépen. Olyan díjmentes verziója, a népszerű IDE, amelynek tesztelési célra használja, és a csapatok is használhat. Tekintse meg a [licencfeltételek](https://www.visualstudio.com/support/legal/mt171547). 

Az asztali ikonra duplán kattintva nyissa meg a Visual Studio vagy a **Start** menü. Keresse meg a programok (Win + S), majd a **Visual Studio**. Itt például C#, Python, R és node.js nyelven projekteket hozhat létre. Telepített modulok könnyítse meg az alábbi Azure-szolgáltatások működéséhez:
* Azure Data Catalog
* Az Azure HDInsight Hadoop és Spark
* Azure Data Lake 

Emellett van egy beépülő modul nevű ```Visual Studio Tools for AI``` , amely zökkenőmentesen integrálható az Azure Machine Learning, és segít gyorsan hozhat létre AI-alkalmazások. 

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet, hogy a próbaidőszak lejárt. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új, hozzáférhet a Visual Studio Community ingyenes fiókot. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
Egy Machine Learning-szolgáltatások adatbázison belüli elemzések futtatására az SQL Server 2017 developer verzióját biztosítja a virtuális gépen, az R vagy Python. Machine Learning-szolgáltatások fejlesztéséhez és üzembe helyezéséhez intelligens alkalmazások platformot biztosít. Ezeket a nyelveket és a Közösségtől számos csomagok segítségével modelleket hozhat létre és előrejelzések készítése az SQL Server-adatok. Elemzési adatokhoz közel képes tartani, mivel a Machine Learning-szolgáltatások, az adatbázis-, integrálható az SQL Server-kiszolgálón belül az R- és Python nyelveket. Ez az integráció kiküszöböli a költségek és az adatáthelyezés biztonsági kockázatokat.

> [!NOTE]
> Az SQL Server Developer edition csak fejlesztési és tesztelési célokat szolgál. Éles környezetben futtatásához licenc szükséges. 
> 
> 

Az SQL Server a Microsoft SQL Server Management Studio elindításával érheti el. A virtuális gép neve fel van töltve, mint a **kiszolgálónév**. Windows-hitelesítést használ a jelentkezik be, a rendszergazda a Windows. Amikor az SQL Server Management Studióban, más felhasználók létrehozása, adatbázisok létrehozása, adatok importálása, és futtasson SQL-lekérdezéseket. 

Engedélyezése az adatbázison belüli elemzések SQL Machine Learning-szolgáltatások használatával, akkor futtassa a következő parancsot, az SQL Server Management Studióban egy egyszeri művelet, miután kiszolgálói rendszergazdaként jelentkezzen be: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Több Azure-eszközök telepítve vannak a virtuális gépen:

* Asztali parancsikon kerül az Azure SDK dokumentációját. 
* **Az AzCopy** történő áthelyezéséhez az adatok átviteléhez az Azure Storage-fiókot használja. Használati megtekintéséhez írja be a **Azcopy** parancsot a parancssorba. 
* Használat **Azure Storage Explorer** , keresse meg az objektumokat az Azure Storage-fiókban tárolja. Adat- és Azure Storage-ból is átvitele. Ez az eszköz eléréséhez, megadhat **Tártallózó** a a **keresési** mező. Keresés a Windows vagy **Start** menü. 
* **Az Adlcopy** helyez át adatokat az Azure Data Lake. Használati megtekintéséhez írja be a **adlcopy** parancsot. 
* **dtui** helyez át adatokat, és az Azure Cosmos DB egy NoSQL-adatbázis a felhőben. Adja meg **dtui** parancsot. 
* **Az Azure Data Factory integrációs modul** helyez át adatokat a helyszíni adatforrások és a felhő között. Eszközök, mint például az Azure Data Factory belül használható. 
* **A Microsoft Azure PowerShell** olyan eszköz, amely a PowerShell szkriptelési nyelv az Azure-erőforrások felügyeletére szolgál. Azt is telepítve van a virtuális Gépen. 

### <a name="power-bi"></a>Power BI
A **Power BI Desktop** irányítópultokat és vizualizációkat hozhat létre segítségével telepítve van. Az eszköz használható az adatok különböző forrásokból származó az irányítópultokat és jelentéseket készíthet, és közzéteheti őket a felhőbe. További információkért lásd: a [Power BI](https://powerbi.microsoft.com) hely. A Power BI desktop megtalálhatja a **Start** menü. 

> [!NOTE]
> Egy Power BI eléréséhez a Microsoft Office 365-fiók szükséges. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Az Azure Machine Learning Workbench egy asztali alkalmazás és parancssori felület. A workbench beépített adat-előkészítés, amely megtanulja az adat-előkészítési lépéseket, ezeket is rendelkezik. Is biztosít a projektmenedzsment, a futtatási előzmények vagy a jegyzetfüzet-integráció a hatékonyság növelése érdekében. 

Használhatja a nyílt forráskódú keretrendszereket is, a TensorFlow, a Cognitive Toolkit, a Spark Machine Learning és a scikit-ismerje meg, a modellek fejlesztésére. A dsvm-hez, a biztosítunk egy asztali ikonra az Azure Machine Learning Workbench telepítése az egyes felhasználói csoportokban **% LOCALAPPDATA %** könyvtár. 

A munkaterület minden felhasználó végre kell hajtania egy egyszeri művelet. Kattintson duplán a ```AzureML Workbench Setup``` asztali ikonra a workbench-példány telepítése. Az Azure Machine Learning is hoz létre, illetve egy felhasználó Python-környezetet, amely bontja ki / a **%LOCALAPPDATA%\amlworkbench\python** könyvtár.

## <a name="more-microsoft-development-tools"></a>További Microsoft fejlesztési eszközök
A [Microsoft Webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx) keresse meg és más Microsoft fejlesztői eszközöket tölthet le. Emellett van egy parancsikont a Microsoft adatelemző virtuális gép asztalán a megadott eszköz.  

## <a name="important-directories-on-the-vm"></a>A virtuális gép fontos könyvtárak
| Elem | Címtár |
| --- | --- |
| Jupyter Notebook server konfigurációk | C:\ProgramData\jupyter |
| Jupyter Notebook minták kezdőkönyvtár | c:\dsvm\notebooks és c:\users\<username > \notebooks |
| Más minták | c:\dsvm\samples |
| Anaconda, alapértelmezett: Python 3.6-os | c:\Anaconda |
| Anaconda Python 2.7-es környezetben | c:\Anaconda\envs\python2 |
| Microsoft Machine Learning-kiszolgálója (önálló) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Az R alapértelmezett példány, Machine Learning-kiszolgáló (önálló) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Machine Learning-szolgáltatások az SQL-adatbázis-példány könyvtár | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Az Azure Machine Learning Workbench, felhasználónként | %localappdata%\amlworkbench | 
| Egyéb eszközök | c:\dsvm\tools |

> [!NOTE]
> A dsvm-hez és Windows Server 2016 edition 2018 március előtti Windows Server 2012 kiadásán alapértelmezett Anaconda környezete Python 2.7-t. A másodlagos környezet a Python 3.5, elhelyezés **c:\Anaconda\envs\py35**. 
> 
> 

## <a name="next-steps"></a>További lépések

* Fedezze fel az eszközök az adatelemző virtuális gép kiválasztásával a **Start** menü.
* Ismerje meg a termék ellátogat az Azure Machine Learning-szolgáltatások és a Workbench [rövid és oktatóanyagok lap](../service/index.yml). 
* Navigáljon a **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** , amely a RevoScaleR kódtára, amely támogatja az adatelemzés, a nagyvállalati szintű R a minták.  
* A cikk a [tíz dolog, amire alkalmas az adatelemző virtuális gép](https://aka.ms/dsvmtenthings).
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml).
* Látogasson el a [Azure AI-katalógusban](http://gallery.cortanaintelligence.com) machine learning és a data analytics minták az Azure Machine Learning és a kapcsolódó adatokat használó szolgáltatások az Azure-ban. Is biztosítunk egy ikont a tárban lévő a **Start** menüjében és asztalán a virtuális gép.


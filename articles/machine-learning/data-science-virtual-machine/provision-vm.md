---
title: A Windows Data Science virtuális gép létrehozása
titleSuffix: Azure
description: Konfigurálja és a egy adatelemző virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: gokuma
ms.openlocfilehash: 0b8349c6c59ac64be15c75b28c083a4483f2f68a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57896262"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>A Windows adatelemző virtuális gép Azure-beli üzembe helyezése

A Microsoft Windows adatelemzési virtuális gép (DSVM) egy Windows Server 2016 virtuális gép (VM) lemezképet az Azure-ban, amely előre telepítve és konfigurálva adatelemzési és gépi tanulási eszközökkel.

## <a name="included-data-science-tools"></a>Szolgáltatási keretbe foglalt adatmennyiség adatelemzési eszközök

A következő eszközök szerepelnek a dsvm-hez:

* [Az Azure Machine Learning szolgáltatás](../service/index.yml) Python SDK-t
* [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/machine-learning-server/index) Developer edition
* Anaconda Python elosztási
* Az R, Python és a PySpark kernelt Jupyter Notebook
* Microsoft Visual Studio Community
* A Microsoft Power BI Desktopban
* A Microsoft SQL Server 2017 Developer edition
* Egy önálló Apache Spark-példányt a helyi fejlesztési és tesztelési célra
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning és az adatok elemzési eszközök:
  * Deep learning-keretrendszerek - AI keretrendszerek széles skáláját tartalmazza a virtuális gépen: [A Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, és Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – gyors machine learning-rendszer, amely támogatja az online kivonatoláshoz, allreduce, csökkentésének, learning2search és aktív és interaktív tanulási eljárások
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) -egy eszköz, gyors és pontos gyorsított fa végrehajtása
  * [Rattle](https://togaware.com/rattle/) – az R analitikai eszközt, amely megkönnyíti az adatok elemzési és gépi tanulási az r használatába Ez magában foglalja a GUI-alapú adatáttekintés és modellezés az R-kód automatikus generálása.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) -visual adatbányászat és a machine learning-szoftver Java nyelven
  * [Apache Drill](https://drill.apache.org/) -séma nélküli SQL lekérdezési motorja Apache Hadoop, a nosql-alapú és a felhőalapú tárolást. A nosql-alapú, és a standard szintű BI eszközök, Power BI, a Microsoft Excel és a Tableau fájlok ODBC, JDBC és felületek támogatja.
* Az R és Python for kódtárak használata az Azure Machine Learning és más Azure-szolgáltatások
* A Git, beleértve a Git bash eszközt, például a GitHub és az Azure DevOps forráskódtárházak dolgozhat. A Git nyújt számos népszerű Linux parancssor eszközök, amelyek elérhetők mind a Git Bash és a egy parancssort. Példák awk, a csökkentésének, a perl, a grep, a keresés, a wget és a curl.

### <a name="about-data-science"></a>Adatelemzési információk

Az adatelemzés magában foglalja a feladatok egy sorozatát léptetés:

1. Keresse meg, betöltését és az adatok előfeldolgozása
1. Összeállításához és teszteléséhez modellek
1. A modellek intelligens alkalmazásokban felhasználásra üzembe helyezése

Az adatszakértők a feladatokhoz számos eszközt használja. Kereshet a szoftver megfelelő verzióját, majd töltse le és telepítse őket a időigényes lehet. A DSVM időt takaríthat meg azáltal, hogy egy használatra kész-rendszerképet, amelyek kioszthatóak az Azure-ban előtelepített és konfigurált számos népszerű eszközzel.

A dsvm-hez az elemzési projekt jump-starts. A feladatok különféle nyelveken, köztük a R, Python, SQL és C# dolgozhat. A Visual Studio fejlesztése és tesztelése a kód egyszerűen használható integrált fejlesztési környezetben (IDE) biztosít. Az Azure SDK-t a virtuális gép szerepel, így Ön könnyedén létrehozhatja az alkalmazások a Microsoft felhőalapú platformja különböző szolgáltatások használatával.

Nincsenek szoftvereket díjmentesen használható a data science Virtuálisgép-rendszerképet. Csak az Azure-használati díjat kell fizetnie. A virtuális gép üzembe helyezésekor méretétől függnek. További részleteket a számítási díjak a **díjszabás** szakaszában a [adatelemző virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) lapot.

### <a name="other-dsvm-versions"></a>Más DSVM-verziók

* Egy [Ubuntu](dsvm-ubuntu-intro.md) kép. A dsvm-hez hasonló számos eszközt, valamint néhány további mélytanulási keretrendszerekkel rendelkezik.
* A [Linux CentOS](linux-dsvm-intro.md) kép.
* A [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) , az adatelemzési virtuális gépet. Néhány eszközök csak a Windows Server 2016 rendszeren érhetők el. Ez a cikk ellenkező esetben is érvényes a Windows Server 2012 verzióra.

## <a name="prerequisite"></a>Előfeltétel

A Microsoft adatelemző virtuális gép létrehozásához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.com/free).

## <a name="create-your-dsvm"></a>A dsvm-hez létrehozása

A DSVM-példány létrehozása:

1. Nyissa meg a virtuális gépet, az ajánlati a [az Azure portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Az Azure-fiókkal bejelentkezni, ha már nincs bejelentkezve kérheti.
1. Válassza ki a **létrehozás** gombra a lap alján.

   ![Konfigurálja-data-adatelemzési – a virtuális gép](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Adja meg a következő, konfigurálja a jobb oldali panelen a képernyőfelvételen a bemutatott lépések szükségesek:

   1. **Alapvető beállítások**:
      * **Név**: a DSVM neve
      * **Virtuális merevlemez típusa**: vagy **SSD** vagy **HDD**. Egy hasonló NVidia Tesla K80-alapú NC_v1 GPU-példány, válassza ki a **HDD** , a lemez típusát.
      * **Felhasználónév**: a rendszergazdai fiók azonosítója
      * **Jelszó**: a rendszergazdafiók jelszavát
      * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.
      * **Erőforráscsoport**. Létrehozhat egy új vagy egy meglévő csoportot.
      * **Hely**. Válassza ki az Adatközpont leginkább megfelelő. A leggyorsabb hálózati hozzáféréshez az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a fizikai helyéhez legközelebbi.
   1. **Méret**: Válassza ki a kiszolgáló típusát, amely megfelel a funkcionális követelmények és költségek megkötések. További lehetőségek a Virtuálisgép-méretek, válasszon **nézet összes**.
   1. **Beállítások**:  
      * **Felügyelt lemezek használata**. Válasszon **felügyelt** Ha azt szeretné, hogy Azure-lemezek kezelése a virtuális gép számára. Ha nem, akkor meg kell adnia egy új vagy meglévő tárfiókot.  
      * **Más paramétereket**. Az alapértelmezett értékeket is használhat. Ha nem az alapértelmezett értékeket használja, a kurzort az egyes mezőkkel tájékoztató mutató hivatkozás.
   1. **Összefoglalás**: Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> * A virtuális Gépet nem kell a számítási költség a kiválasztott kiszolgáló méret túl további díjat fizetniük a **mérete** . lépés.
> * Üzembe helyezési idő körülbelül 10 és 20 perc szükséges. A virtuális gép állapotát az Azure Portalon tekintheti meg.

## <a name="how-to-access-the-dsvm"></a>A DSVM elérése

Miután a virtuális gép létrehozása és üzembe helyezett, segítségével a távoli asztal bele a rendszergazdai fiók hitelesítő adatait, amely az előző konfigurált **alapjai** szakaszban. Készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. Számos olyan eszközzel start menü csempék és asztali ikonok keresztül érhetők el.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gépen telepített eszközök

További információ az eszközök, amelyek a dsvm-hez telepítve:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Használhatja a Microsoft Enterprise Library méretezhető R vagy Python elemzési, mert a Machine Learning Server Developer edition telepítve van a virtuális gépen. Korábbi nevén Microsoft R Server, a Machine Learning-kiszolgáló egy nagyjából-üzembe helyezhető, nagyvállalati szintű elemzési platform. R és Python is elérhető legyen. Célszerű is méretezhető, üzleti szempontból által támogatott és biztonságos.

Machine Learning-kiszolgáló támogatja a különböző big data típusú statisztikai, prediktív modellezési és gépi tanulási feladatok. Az elemzések széles skálását támogatja: feltárás, elemzés, vizualizációs és modellezés. Szerint használ, és kiterjeszti a nyílt forráskódú R és Python, a Machine Learning-kiszolgáló a kompatibilis az R és Python-parancsfájlokban és függvényekben. Emellett akkor is kompatibilis a CRAN, a pipet és a Conda-csomagok az adatok a nagyvállalati szintű elemzésére.

Machine Learning-kiszolgáló hozzáadásával a párhuzamos és darabolásos adatok feldolgozása a memórián belüli korlátozások, nyílt forráskódú R címek. Ez azt jelenti, hogy elemzéseket végezhet, mint a fő memóriában adatfeldolgozással sokkal nagyobb méretű adatokon. A Visual Studio Community tartalmazza a virtuális gépen. Az R tools for Visual Studio és a Python Tools, adjon meg egy teljes IDE-R vagy Python használata a Visual Studio (PTVS)-bővítmények rajta. Is biztosítanak, mint más IDEs [RStudio](http://www.rstudio.com) és [PyCharm Community edition](https://www.jetbrains.com/pycharm/) a virtuális gépen.

### <a name="python"></a>Python

-Fejlesztéshez a Python 2.7-es és 3.6-os Anaconda Python disztribúciók települnek. Ezek a disztribúciók körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python rendelkezik. Projektek készítése PTVS, amelyre telepítve van a Visual Studio Community 2017 is használhatja. Vagy az Ide-k, például TÉTLEN vagy Spyder Anaconda mellékelhető egyikét használhatja. Keresse meg és indítsa el ezeket a csomagokat (Win + S) egyikét.

> [!NOTE]
> Mutasson a Python Tools for Visual Studio, az Anaconda Python 2.7-es, szeretne létrehozni az egyéni környezet minden verzióhoz. E környezet elérési útvonalak beállítása a Visual Studio 2017 Community, navigáljon a **eszközök** > **Python Tools** > **Python-környezetek**. Válassza ki **+ egyéni**.

Anaconda Python 3.6-os verziója telepítve van a **C:\Anaconda**. Anaconda Python 2.7-es telepítőmappájában **c:\Anaconda\envs\python2**. Részletes lépéseiért lásd: [PVTS dokumentációban](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>A Jupyter Notebook

A Jupyter Notebookot, megosztani a kódot és az elemzési környezet anaconda terjesztési is tartalmaz. A Jupyter Notebook kiszolgáló előre konfigurálva vannak a Python 2.7-es, Python 3.x, PySpark, Julia és R kernelekkel. Indítsa el a Jupyter-kiszolgálót, és indítsa el a böngészőt a jegyzetfüzet-kiszolgálóhoz való hozzáféréshez, használja a **Jupyter Notebook** asztali ikonra.

Mi a Python és az r segítségével számos mintafüzetek csomag Jupyter éri el, miután a notebookok megjelenítése a következő technológiákat használata:

* Machine Learning Server
* Az SQL Server Machine Learning szolgáltatás adatbázison belüli elemzések
* Python
* Microsoft Cognitive Toolkittel
* Tensorflow
* Más Azure-technológiák

Annak a minták mutató hivatkozást a jegyzetfüzet kezdőlap után a korábbi lépésben létrehozott jelszó használatával hitelesítést a Jupyter Notebookot.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

A dsvm-hez a Visual Studio Community tartalmazza. Ez az olyan díjmentes verziója, a népszerű IDE, amelynek tesztelési célra használja, és a csapatok is használhat. Tekintse meg a [licencfeltételek](https://www.visualstudio.com/support/legal/mt171547).

Nyissa meg a Visual Studio az Asztal ikon használatával vagy a **Start** menü. Keresse meg a programok (Win + S), majd a **Visual Studio**. Itt például C#, Python, R és node.js nyelven projekteket hozhat létre. Telepített modulok könnyítse meg az alábbi Azure-szolgáltatások működéséhez:

* Azure Data Catalog
* Az Azure HDInsight Hadoop és Spark
* Azure Data Lake

Emellett van egy beépülő modul nevű **a Visual Studio Code az Azure Machine Learning** , amely zökkenőmentesen integrálható az Azure Machine Learning, és segít gyorsan hozhat létre AI-alkalmazások.

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet, hogy a próbaidőszak lejárt. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új, hozzáférhet a Visual Studio Community ingyenes fiókot.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

A DSVM tartalmaz egy Machine Learning-szolgáltatások az SQL Server 2017 developer verzióját. Az SQL Server jelen kiadása vagy R vagy Python nyelven érhető el, és futtathatja az adatbázison belüli elemzések. Machine Learning-szolgáltatások fejlesztéséhez és üzembe helyezéséhez intelligens alkalmazások platformot biztosít. Ezeket a nyelveket és a Közösségtől számos csomagok segítségével modelleket hozhat létre és előrejelzések készítése az SQL Server-adatok. Elemzési adatokhoz közel képes tartani, mivel a Machine Learning-szolgáltatások, az adatbázis-, integrálható az SQL Server-kiszolgálón belül az R- és Python nyelveket. Ez az integráció kiküszöböli a költségek és az adatáthelyezés biztonsági kockázatokat.

> [!NOTE]
> Az SQL Server Developer edition csak fejlesztési és tesztelési célokat szolgál. Éles környezetben futtatásához licenc szükséges.

Az SQL Server a Microsoft SQL Server Management Studio elindításával érheti el. A virtuális gép neve fel van töltve, mint a **kiszolgálónév**. Windows-hitelesítést használ a jelentkezik be, a rendszergazda a Windows. Amikor az SQL Server Management Studióban, más felhasználók létrehozása, adatbázisok létrehozása, adatok importálása, és futtasson SQL-lekérdezéseket.

Engedélyezése az adatbázison belüli elemzések SQL Machine Learning-szolgáltatások használatával, akkor futtassa a következő parancsot, az SQL Server Management Studióban egy egyszeri művelet, miután kiszolgálói rendszergazdaként jelentkezzen be:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Cserélje le `%COMPUTERNAME%` a virtuális gép nevére.

### <a name="azure"></a>Azure

Több Azure-eszközök telepítve vannak a virtuális gépen:

* Asztali parancsikon kerül az Azure SDK dokumentációját.
* Használat **AzCopy** másolása az adatok átviteléhez az Azure Storage-fiókot. Használati megtekintéséhez írja be a **Azcopy** parancsot a parancssorba.
* Használat **Azure Storage Explorer** , keresse meg az objektumokat az Azure Storage-fiókban tárolja. Azt is adatokat másol az Azure Storage szolgáltatásba vagy onnan. Adja meg az eszköz eléréséhez **Tártallózó** a a **keresési** mező. Vagy a Windows az található **Start** menü.
* **Az Adlcopy** másolnak adatokat az Azure Data Lake. Használati megtekintéséhez írja be a **adlcopy** parancsot.
* **dtui** másolja az adatokat, és az Azure Cosmos DB egy NoSQL-adatbázis a felhőben. Adja meg **dtui** parancsot.
* **Az Azure Data Factory integrációs modul** másolja az adatokat a helyszíni adatforrások és a felhő között. Eszközök, mint például az Azure Data Factory belül használható.
* Használat **a Microsoft Azure PowerShell** a PowerShell szkriptelési nyelv az Azure-erőforrások felügyeletére. Azt is telepítve van a virtuális Gépen.

### <a name="power-bi"></a>Power BI

A dsvm-hez mellékelt **Power BI Desktop** irányítópultokat és vizualizációkat hozhat létre segítségével telepítve. Az eszköz használható az adatok különböző forrásokból származó az irányítópultokat és jelentéseket készíthet, és közzéteheti őket a felhőbe. További információkért lásd: a [Power BI](https://powerbi.microsoft.com) hely. A Power BI Desktop talál a **Start** menü.

> [!NOTE]
> Egy Power BI eléréséhez a Microsoft Office 365-fiók szükséges.

### <a name="azure-machine-learning-service-python-sdk"></a>Az Azure Machine Learning szolgáltatás Python SDK-t

Az adatszakértők és a Mesterségesintelligencia-fejlesztőknek használata Azure Machine Learning-SDK Pythonhoz készült való létrehozásához és futtatásához a machine learning munkafolyamatok a [az Azure Machine Learning szolgáltatás](../service/overview-what-is-azure-ml.md). Használhatja a szolgáltatást, a Jupyter notebookok vagy egy másik Python IDE használatával nyílt forráskódú keretrendszerekkel, mint például a tensorflow-hoz és a scikit-megtudhatja.

A Python SDK használatának első lépései, lásd: [az Azure Machine Learning használatának első lépései a Python](../service/quickstart-create-workspace-with-python.md).

A Python SDK telepítve van a Microsoft Data Science virtuális gépen.

## <a name="more-microsoft-development-tools"></a>További Microsoft fejlesztési eszközök

Használhatja a [Microsoft Webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx) keresése és más Microsoft fejlesztői eszközöket tölthet le. Emellett van egy parancsikont a Microsoft adatelemző virtuális gép asztalán eszköz.  

## <a name="important-directories-on-the-vm"></a>A virtuális gép fontos könyvtárak

| Elem | Címtár |
| --- | --- |
| Jupyter Notebook server konfigurációk | C:\ProgramData\jupyter |
| Jupyter Notebook minták kezdőkönyvtár | C:\dsvm\notebooks és c:\users\\< felhasználónév\>\notebooks |
| Más minták | c:\dsvm\samples |
| Anaconda, alapértelmezett: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7-es környezetben | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning-kiszolgálója (önálló) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Az R alapértelmezett példány, Machine Learning-kiszolgáló (önálló) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Machine Learning-szolgáltatások az SQL-adatbázis-példány könyvtár | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Egyéb eszközök | c:\dsvm\tools |

> [!NOTE]
> A dsvm-hez és Windows Server 2016 edition 2018 március előtti Windows Server 2012 kiadásán alapértelmezett Anaconda környezete Python 2.7-t. A másodlagos környezet a Python 3.5, elhelyezés **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>További lépések

* Fedezze fel az eszközök az adatelemző virtuális gép kiválasztásával a **Start** menü.
* Ismerje meg az Azure Machine Learning szolgáltatással kapcsolatban, olvassa el [Mi az Azure Machine Learning szolgáltatás?](../service/overview-what-is-azure-ml.md) , illetve próbálhatja ki a [útmutatóink és oktatóanyagaink segítségével](../service/index.yml) elérhető.
* A Fájlkezelőben keresse meg **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** , amely a RevoScaleR kódtára, amely támogatja az adatelemzés, a nagyvállalati szintű R a minták.  
* A cikk a [tíz dolog, amire alkalmas az adatelemző virtuális gép](https://aka.ms/dsvmtenthings).
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml).
* Látogasson el a [Azure AI-katalógusban](https://gallery.cortanaintelligence.com) machine learning és a data analytics minták az Azure Machine Learning és a kapcsolódó adatokat használó szolgáltatások az Azure-ban. Is biztosítunk egy ikont a tárban lévő a **Start** menüjében és asztalán a virtuális gép.

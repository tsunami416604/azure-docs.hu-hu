---
title: 'Gyors útmutató: Windows létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurálja és a egy adatelemző virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: ac4b9b4d32d05083ceabd41207243eb483648baa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278591"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>Gyors útmutató: Windows Data Science Virtual Machine beállítása az Azure-ban

A Microsoft Windows Data Science Virtual Machine (DSVM) egy Windows Server 2016 virtuális gép (VM) rendszerkép az Azure-ban. A szolgáltatás előre telepítve és konfigurálva van az adatelemzési és a gépi tanulási eszközökkel.

## <a name="included-data-science-tools"></a>Tartalmazott adatelemzési eszközöket

A DSVM a következő eszközöket tartalmazza:

* Python SDK a [Azure Machine learning szolgáltatáshoz](../index.yml).
* [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/machine-learning-server/index) Developer Edition szoftverhez.
* Anaconda Python elosztási.
* Jupyter Notebook az R, Python és a PySpark kernelt.
* A Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* A Microsoft SQL Server 2017 Developer edition.
* Egy önálló Apache Spark-példányt helyi fejlesztési és tesztelési célra.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning és az adatok elemzési eszközök:
  * Deep learning-keretrendszerek: A virtuális gép a [TensorFlow](https://www.tensorflow.org/), a [chainer](https://chainer.org/), a MXNET és a kerasz AI-keretrendszereket is tartalmazza.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Gyors machine learning-rendszer, amely támogatja az online kivonatoláshoz, allreduce, csökkentésének, learning2search és aktív és interaktív tanulási eljárások.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Egy eszköz, gyorsított fa gyors és pontos végrehajtását.
  * [Csörgő](https://togaware.com/rattle/). Az r analitikai eszköz, amely az R-ben megkezdi az adatelemzést és a gépi tanulást. Ez magában foglalja a GUI-alapú adatelemzést és-modellezést automatikus R-kód generálásával.
  * [WEKA](https://www.cs.waikato.ac.nz/ml/weka/): Vizuális adatbányászat és a gépi tanulási Java szoftvert.
  * [Apache-részletezés](https://drill.apache.org/): Séma nélküli SQL lekérdezési motorja Apache Hadoop, a nosql-alapú és a felhőalapú tárolást. A nosql-alapú, és a standard szintű BI eszközök, Power BI, a Microsoft Excel és a Tableau fájlok ODBC, JDBC és felületek támogatja.
* Kódtárak R és Python esetében az Azure Machine Learning és más Azure-szolgáltatásokat használja.
* Git, beleértve a git Bashet, hogy működjön a forráskód-Tárházak, például a GitHub és az Azure DevOps. A git számos népszerű linuxos parancssori eszközt biztosít, amelyek a git Bashben és a parancssorban egyaránt elérhetők. Példák awk, a csökkentésének, a perl, a grep, a keresés, a wget és a curl.
* Fejlesztői eszközök és szerkesztők (RStudio, Notebookshoz).

### <a name="about-data-science"></a>Tudnivalók az adattudományról

Az adatelemzés magában foglalja a feladatok egy sorozatát léptetés:

1. Keresse meg, betöltését és az adatok előfeldolgozása.
1. Hozhat létre, és tesztelni tudtuk.
1. A modellek intelligens alkalmazásokban felhasználásra üzembe helyezése.

Az adatszakértők a feladatokhoz számos eszközt használja. Kereshet a szoftver megfelelő verzióját, majd töltse le és telepítse őket a időigényes lehet. A DSVM időt takaríthat meg az Azure-ban üzembe helyezhető, használatra kész, előre telepített és konfigurált népszerű eszközökkel.

A DSVM Jump-elindítja az elemzési projektet. A feladatok különböző nyelveken, például az R, a Python, az SQL és C#a használatával dolgozhatók. A Visual Studio fejlesztése és tesztelése a kód egyszerűen használható integrált fejlesztési környezetben (IDE) biztosít. Az Azure SDK-t a virtuális gép tartalmazza, így a Microsoft Cloud platformon elérhető szolgáltatások segítségével hozhatja létre alkalmazásait.

Ehhez a DSVM-rendszerképhez nem tartoznak szoftveres díjak. Csak az Azure-használati díjat kell fizetnie. A kiépített virtuális gép méretétől függenek. További információ a [Data Science Virtual Machine oldalon](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)található.

### <a name="other-dsvm-versions"></a>Egyéb DSVM-verziók

* Egy [Ubuntu](dsvm-ubuntu-intro.md) kép. A DSVM hasonló eszközökkel rendelkezik, és néhány mély tanulási keretrendszert is tartalmaz.
* A [Linux CentOS](linux-dsvm-intro.md) kép.
* A [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) , az adatelemzési virtuális gépet. Néhány eszközök csak a Windows Server 2016 rendszeren érhetők el. Ez a cikk ellenkező esetben is érvényes a Windows Server 2012 verzióra.

## <a name="prerequisite"></a>Előfeltétel

A Microsoft adatelemző virtuális gép létrehozásához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.com/free).

## <a name="create-your-dsvm"></a>A DSVM létrehozása

DSVM-példány létrehozása:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)virtuális gép listáját. Előfordulhat, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-fiókjába, ha még nincs bejelentkezve.
1. Válassza a **Létrehozás** gombot alul.

   ![Virtuális gépek listázása a Azure Portal a létrehozás gombbal](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Adja meg a következő adatokat a képernyőkép jobb oldali ablaktábláján megjelenő lépések konfigurálásához:

   1. **Alapvető beállítások**:
      * **Név**: Adja meg a DSVM nevét.
      * **VM-lemez típusa**: Válassza az **SSD** vagy a **HDD**lehetőséget. A NC_v1 GPU-példányok, például az NVIDIA Tesla K80 alapján válassza a **HDD** lehetőséget a lemez típusaként.
      * **Felhasználónév**: Adja meg a rendszergazdai fiók AZONOSÍTÓját.
      * **Jelszó**: Adja meg a rendszergazdai fiók jelszavát.
      * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki azt a számítógépet, amelyet a gép létrehoz és számláz.
      * **Erőforráscsoport**: Hozzon létre egy új csoportot, vagy használjon egy meglévőt.
      * **Hely**: Válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáféréshez ez az adatközpont, amely a legtöbb adattal rendelkezik, vagy a legközelebb áll a fizikai helyhez.
   1. **Méret**: Válassza ki a kiszolgálói típust, amely megfelel a funkcionális követelményeknek és a költséghatékonysági korlátozásoknak. További lehetőségek a Virtuálisgép-méretek, válasszon **nézet összes**.
   1. **Beállítások**:  
      * **Felügyelt lemezek használata**. Válasszon **felügyelt** Ha azt szeretné, hogy Azure-lemezek kezelése a virtuális gép számára. Ha nem, akkor meg kell adnia egy új vagy meglévő tárfiókot.  
      * **Más paramétereket**. Az alapértelmezett értékeket is használhat. Ha nem alapértelmezett értékeket szeretne használni, vigye az egérmutatót az információs hivatkozás fölé az adott mezők súgójában.
   1. **Összefoglalás**: Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> * A virtuális gép nem számít fel további díjat a **méret** lépésben kiválasztott kiszolgáló méretére vonatkozó számítási költségeken túl.
> * A kiépítés 10 – 20 percet vesz igénybe. A virtuális gép állapotát a Azure Portal tekintheti meg.

## <a name="access-the-dsvm"></a>A DSVM elérése

A virtuális gép létrehozása és üzembe helyezése után távoli asztali kapcsolaton keresztül férhet hozzá. Használja a virtuális gép létrehozásának **alapjai** lépésében konfigurált rendszergazdai fiók hitelesítő adatait. 

Készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. A **Start** menü csempéi és asztali ikonjai számos eszközt is elérhet.

Emellett DSVM is csatolhat Azure Notebooks a Jupyter notebookok futtatásához a virtuális gépen, és megkerülheti az ingyenes szolgáltatási szintek korlátozásait. További információ: [notebook-projektek kezelése és konfigurálása](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>A DVSM telepített eszközök

A következő részben további információkat talál a Data Science Virtual Machine telepített eszközökről.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Az elemzéshez használhatja a Microsoft Enterprise Library-t, mivel Machine Learning Server Developer Edition telepítve van a virtuális gépre. Korábbi nevén Microsoft R Server, Machine Learning Server egy széles körben telepíthető elemzési platform. Méretezhető és kereskedelmileg támogatott.

Machine Learning-kiszolgáló támogatja a különböző big data típusú statisztikai, prediktív modellezési és gépi tanulási feladatok. Az elemzések széles skálását támogatja: feltárás, elemzés, vizualizációs és modellezés. Szerint használ, és kiterjeszti a nyílt forráskódú R és Python, a Machine Learning-kiszolgáló a kompatibilis az R és Python-parancsfájlokban és függvényekben. Emellett akkor is kompatibilis a CRAN, a pipet és a Conda-csomagok az adatok a nagyvállalati szintű elemzésére.

Machine Learning-kiszolgáló hozzáadásával a párhuzamos és darabolásos adatok feldolgozása a memórián belüli korlátozások, nyílt forráskódú R címek. Ez azt jelenti, hogy az elemzést sokkal nagyobb adatmennyiségen futtathatja, mint a fő memóriában. 

A Visual Studio Community tartalmazza a virtuális gépen. A Visual Studio és a Python Tools for Visual Studio (PTVS) olyan R Tools-bővítményekkel rendelkezik, amelyek teljes körű IDE-t biztosítanak az R vagy a Python használatához. Egyéb ide-ket is biztosítunk, például a [RStudio](https://www.rstudio.com) és a [notebookshoz Community EDITIONT](https://www.jetbrains.com/pycharm/) a virtuális gépen.

### <a name="python"></a>Python

A Python használatával történő fejlesztéshez a 2,7-es és a 3,6-es anaconda Python-disztribúciók vannak telepítve. Ezek a disztribúciók körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python rendelkezik. A Visual Studio Community 2017-es verzióban telepített PTVS-t használhatja. Vagy használhatja a anaconda, például az inaktív vagy a Spyder. Keresse meg és nyissa meg az alábbi csomagok egyikét (Windows billentyű + S).

> [!NOTE]
> Mutasson a Python Tools for Visual Studio, az Anaconda Python 2.7-es, szeretne létrehozni az egyéni környezet minden verzióhoz. Ha ezeket a környezeti útvonalakat a Visual Studio 2017 közösségében szeretné beállítani, ugorjon a **Tools** > **Python-eszközök** > **Python-környezetek**elemre. Válassza ki **+ egyéni**.

A C:\Anaconda.-ben telepített anaconda Python 3,6 A C:\Anaconda\envs\python2.-ben telepített anaconda Python 2,7 A részletes lépésekért tekintse meg a [PTVS dokumentációját](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>A Jupyter Notebook

Az anaconda Distribution a Jupyter Notebook, egy környezettel is megoszthatja a kódot és az elemzést. A Jupyter Notebook kiszolgáló előre konfigurálva vannak a Python 2.7-es, Python 3.x, PySpark, Julia és R kernelekkel. A Jupyter-kiszolgáló elindításához és a böngésző megnyitásához a notebook-kiszolgáló eléréséhez használja a **Jupyter notebook** asztal ikonját.

Mi a Python és az r segítségével számos mintafüzetek csomag Jupyter éri el, miután a notebookok megjelenítése a következő technológiákat használata:

* Machine Learning Server
* SQL Server Machine Learning Services, adatbázis-elemzés
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Egyéb Azure-technológiák

A mintákra mutató hivatkozás a jegyzetfüzet kezdőlapján található, miután a korábban létrehozott jelszó használatával megtalálta a hitelesítést a Jupyter Notebook.

### <a name="visual-studio-community-2017"></a>A Visual Studio Community 2017.

A DSVM tartalmazza a Visual Studio Community-t. Ez a Microsoft népszerű IDE-verziójának ingyenes verziója, amelyet kiértékelési célokra és kis csapatoknak is használhatnak. Tekintse meg a [Microsoft szoftverlicenc-szerződését](https://www.visualstudio.com/support/legal/mt171547).

Nyissa meg a Visual studiót az asztal ikon vagy a **Start** menü használatával. Keressen programokat (Windows billentyű + S), majd a **Visual studiót**. Innen olyan nyelveket hozhat létre, mint C#a, a Python, az R és a Node. js. Telepített modulok könnyítse meg az alábbi Azure-szolgáltatások működéséhez:

* Azure Data Catalog
* Azure-HDInsight a Hadoop és a Sparkhoz
* Azure Data Lake

A Visual Studio Code-hoz Azure Machine Learning nevű beépülő modul is integrálódik a Azure Machine Learning, és segít a mesterséges intelligenciát használó alkalmazások gyors kiépítésében.

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet, hogy a próbaidőszak lejárt. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új, hozzáférhet a Visual Studio Community ingyenes fiókot.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

A DSVM a SQL Server 2017 fejlesztői verziójával jön Machine Learning Services. Ez a SQL Server-kiadás R vagy Python nyelven érhető el, és képes az adatbázis-elemzések futtatására. 

Machine Learning-szolgáltatások fejlesztéséhez és üzembe helyezéséhez intelligens alkalmazások platformot biztosít. Ezeket a nyelveket és a Közösségtől számos csomagok segítségével modelleket hozhat létre és előrejelzések készítése az SQL Server-adatok. Elemzési adatokhoz közel képes tartani, mivel a Machine Learning-szolgáltatások, az adatbázis-, integrálható az SQL Server-kiszolgálón belül az R- és Python nyelveket. Ez az integráció kiküszöböli a költségek és az adatáthelyezés biztonsági kockázatokat.

> [!NOTE]
> Az SQL Server Developer edition csak fejlesztési és tesztelési célokat szolgál. Éles környezetben futtatásához licenc szükséges.

A SQL Server Microsoft SQL Server Management Studio megnyitásával érheti el. A virtuális gép neve **kiszolgálónévként**van feltöltve. Windows-hitelesítést használ a jelentkezik be, a rendszergazda a Windows. Amikor az SQL Server Management Studióban, más felhasználók létrehozása, adatbázisok létrehozása, adatok importálása, és futtasson SQL-lekérdezéseket.

Ha SQL Server Machine Learning Services használatával szeretné engedélyezni az adatbázison belüli elemzéseket, futtassa a következő parancsot egyszeri műveletként a SQL Server Management Studio-ben a kiszolgáló-rendszergazdaként való bejelentkezés után:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Cserélje `%COMPUTERNAME%` le a nevet a virtuális gép nevére.

### <a name="azure"></a>Azure

Több Azure-eszközök telepítve vannak a virtuális gépen:

* Asztali parancsikon kerül az Azure SDK dokumentációját.
* Az AzCopy használatával másolhatja az Azure Storage-fiókját és onnan kívüli adatait. Használati megtekintéséhez írja be a **Azcopy** parancsot a parancssorba.
* A Azure Storage Explorer használatával böngészhet az Azure Storage-fiókjában tárolt objektumokon. Emellett az Azure Storage-ba irányuló és onnan másolt adatok is. Az eszköz eléréséhez írja be a **Storage Explorer** **kifejezést a keresőmezőbe** . Keresés a Windows vagy **Start** menü.
* A AdlCopy átmásolja az Azure Data Lakeba. Használati megtekintéséhez írja be a **adlcopy** parancsot.
* A dtui eszköz a felhőben lévő NoSQL-adatbázisba másolja az adatait Azure Cosmos DBba. Adja meg **dtui** parancsot.
* Az Integration Runtime a helyszíni adatforrások és a felhő közötti Adatmásolást végzi. Eszközök, mint például az Azure Data Factory belül használható.
* A Azure PowerShell használatával felügyelheti Azure-erőforrásait a PowerShell programozási nyelvén. Azt is telepítve van a virtuális Gépen.

### <a name="power-bi"></a>Power BI

A DSVM az irányítópultok és a vizualizációk létrehozásához a Power BI Desktop telepítésével jön létre. Az eszköz használható az adatok különböző forrásokból származó az irányítópultokat és jelentéseket készíthet, és közzéteheti őket a felhőbe. További információ: [Power bi-hely](https://powerbi.microsoft.com). Power BI Desktop a **Start** menüben található.

> [!NOTE]
> Egy Power BI eléréséhez a Microsoft Office 365-fiók szükséges.

### <a name="azure-machine-learning-sdk-for-python"></a>A Pythonhoz készült Azure Machine Learning SDK

Az adatszakértők és a mesterséges intelligencia-fejlesztők a Azure Machine Learning SDK for Pythont használják a gépi tanulási munkafolyamatok létrehozásához és futtatásához a [Azure Machine learning szolgáltatással](../service/overview-what-is-azure-ml.md). A szolgáltatást Jupyter jegyzetfüzetekben vagy más Python IDE-ben is használhatja nyílt forráskódú keretrendszerek, például a TensorFlow és a scikit-Learn használatával.

A Python SDK telepítve van a Microsoft Data Science virtuális gépen. A Python SDK használatának megkezdéséhez tekintse meg a [Azure Machine learning használatának első lépései a Python használatával](../service/quickstart-create-workspace-with-python.md)című témakört.

## <a name="more-microsoft-development-tools"></a>További Microsoft fejlesztési eszközök

A Microsoft [webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx) más Microsoft fejlesztői eszközöket is megtalálhat és tölthet le. Az eszközre a Microsoft Data Science Virtual Machine asztalán is található parancsikon.  

## <a name="important-directories-on-the-vm"></a>A virtuális gép fontos könyvtárak

| Elem | Címtár |
| --- | --- |
| Jupyter Notebook server konfigurációk | C:\ProgramData\jupyter |
| Jupyter Notebook minták kezdőkönyvtár | C:\dsvm\notebooks és c:\Users\\< Felhasználónév\>\notebooks |
| Más minták | C:\dsvm\samples |
| Anaconda, alapértelmezett: Python 3,6 | C:\Anaconda |
| Anaconda Python 2.7-es környezetben | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (önálló) a Pythonhoz | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Alapértelmezett R-példány, Machine Learning Server (önálló) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Machine Learning Services adatbázisbeli példányok könyvtára | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Egyéb eszközök | C:\dsvm\tools |

> [!NOTE]
> A DSVM Windows Server 2012 kiadásában és a Windows Server 2016 kiadásban a 2018. március előtt az alapértelmezett anaconda-környezet a Python 2,7. A másodlagos környezet a Python 3,5, amely a következő helyen található: C:\Anaconda\envs\py35.

## <a name="next-steps"></a>További lépések

* A **Start** menü megnyitásával tárja fel a DSVM található eszközöket.
* Ismerkedjen meg a Azure Machine Learning szolgáltatással, olvassa el a [Mi az Azure Machine learning Service?](../service/overview-what-is-azure-ml.md) című témakört, és próbálja ki az [oktatóanyagokat](../index.yml).
* A Fájlkezelőben keresse meg a C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts azokat a mintákat, amelyek az R RevoScaleR-könyvtárat használják, és nagyvállalati szintű adatelemzést támogatnak. 
* Olvassa el a [Data Science Virtual Machineon](https://aka.ms/dsvmtenthings)elvégezhető tíz dolgot.
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml).
* Látogasson el a [Azure AI-katalógusban](https://gallery.cortanaintelligence.com) machine learning és a data analytics minták az Azure Machine Learning és a kapcsolódó adatokat használó szolgáltatások az Azure-ban. A katalógushoz a **Start** menüben és a virtuális gép asztalán is elérhető egy ikon.

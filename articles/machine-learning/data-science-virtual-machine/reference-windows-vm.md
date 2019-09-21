---
title: 'Leírások: Windows DSVM'
description: A Windows Data Science VMban található eszközök részletei
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 8ed083ad39c3ace3a0f66a61936974b462ed418f
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174694"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Leírások: Windows Data Science Virtual Machine

A Windows Data Science Virtual Machine elérhető eszközeinek listáját alább találja. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Az elemzéshez használhatja a Microsoft Enterprise Library-t, mivel Machine Learning Server Developer Edition telepítve van a virtuális gépre. Korábbi nevén Microsoft R Server, Machine Learning Server egy széles körben telepíthető elemzési platform. Méretezhető és kereskedelmileg támogatott.

Machine Learning-kiszolgáló támogatja a különböző big data típusú statisztikai, prediktív modellezési és gépi tanulási feladatok. Az elemzések széles skálását támogatja: feltárás, elemzés, vizualizációs és modellezés. Szerint használ, és kiterjeszti a nyílt forráskódú R és Python, a Machine Learning-kiszolgáló a kompatibilis az R és Python-parancsfájlokban és függvényekben. Emellett akkor is kompatibilis a CRAN, a pipet és a Conda-csomagok az adatok a nagyvállalati szintű elemzésére.

Machine Learning-kiszolgáló hozzáadásával a párhuzamos és darabolásos adatok feldolgozása a memórián belüli korlátozások, nyílt forráskódú R címek. Ez azt jelenti, hogy az elemzést sokkal nagyobb adatmennyiségen futtathatja, mint a fő memóriában. 

A Visual Studio Community tartalmazza a virtuális gépen. A Visual Studio és a Python Tools for Visual Studio (PTVS) olyan R Tools-bővítményekkel rendelkezik, amelyek teljes körű IDE-t biztosítanak az R vagy a Python használatához. Egyéb ide-ket is biztosítunk, például a [RStudio](https://www.rstudio.com) és a [notebookshoz Community EDITIONT](https://www.jetbrains.com/pycharm/) a virtuális gépen.

## <a name="python"></a>Python

A Python használatával történő fejlesztéshez a 2,7-es és a 3,6-es anaconda Python-disztribúciók vannak telepítve. Ezek a disztribúciók körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python rendelkezik. A Visual Studio Community 2017-es verzióban telepített PTVS-t használhatja. Vagy használhatja a anaconda, például az inaktív vagy a Spyder. Keresse meg és nyissa meg az alábbi csomagok egyikét (Windows billentyű + S).

> [!NOTE]
> Mutasson a Python Tools for Visual Studio, az Anaconda Python 2.7-es, szeretne létrehozni az egyéni környezet minden verzióhoz. Ha ezeket a környezeti útvonalakat a Visual Studio 2017 közösségében szeretné beállítani, ugorjon a **Tools** > **Python-eszközök** > **Python-környezetek**elemre. Válassza ki **+ egyéni**.

A C:\Anaconda.-ben telepített anaconda Python 3,6 A C:\Anaconda\envs\python2.-ben telepített anaconda Python 2,7 A részletes lépésekért tekintse meg a [PTVS dokumentációját](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

## <a name="the-jupyter-notebook"></a>A Jupyter Notebook

Az anaconda Distribution a Jupyter Notebook, egy környezettel is megoszthatja a kódot és az elemzést. A Jupyter Notebook kiszolgáló előre konfigurálva vannak a Python 2.7-es, Python 3.x, PySpark, Julia és R kernelekkel. A Jupyter-kiszolgáló elindításához és a böngésző megnyitásához a notebook-kiszolgáló eléréséhez használja a **Jupyter notebook** asztal ikonját.

Mi a Python és az r segítségével számos mintafüzetek csomag Jupyter éri el, miután a notebookok megjelenítése a következő technológiákat használata:

* Machine Learning Server
* SQL Server Machine Learning Services, adatbázis-elemzés
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Egyéb Azure-technológiák

A mintákra mutató hivatkozás a jegyzetfüzet kezdőlapján található, miután a korábban létrehozott jelszó használatával megtalálta a hitelesítést a Jupyter Notebook.

## <a name="visual-studio-community-2017"></a>A Visual Studio Community 2017.

A DSVM tartalmazza a Visual Studio Community-t. Ez a Microsoft népszerű IDE-verziójának ingyenes verziója, amelyet kiértékelési célokra és kis csapatoknak is használhatnak. Tekintse meg a [Microsoft szoftverlicenc-szerződését](https://www.visualstudio.com/support/legal/mt171547).

Nyissa meg a Visual studiót az asztal ikon vagy a **Start** menü használatával. Keressen programokat (Windows billentyű + S), majd a **Visual studiót**. Innen olyan nyelveket hozhat létre, mint C#a, a Python, az R és a Node. js. Telepített modulok könnyítse meg az alábbi Azure-szolgáltatások működéséhez:

* Azure Data Catalog
* Azure-HDInsight a Hadoop és a Sparkhoz
* Azure Data Lake

A Visual Studio Code-hoz Azure Machine Learning nevű beépülő modul is integrálódik a Azure Machine Learning, és segít a mesterséges intelligenciát használó alkalmazások gyors kiépítésében.

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet, hogy a próbaidőszak lejárt. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új, hozzáférhet a Visual Studio Community ingyenes fiókot.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

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

## <a name="azure"></a>Azure

Több Azure-eszközök telepítve vannak a virtuális gépen:

* Asztali parancsikon kerül az Azure SDK dokumentációját.
* Az AzCopy használatával másolhatja az Azure Storage-fiókját és onnan kívüli adatait. Használati megtekintéséhez írja be a **Azcopy** parancsot a parancssorba.
* A Azure Storage Explorer használatával böngészhet az Azure Storage-fiókjában tárolt objektumokon. Emellett az Azure Storage-ba irányuló és onnan másolt adatok is. Az eszköz eléréséhez írja be a **Storage Explorer** **kifejezést a keresőmezőbe** . Keresés a Windows vagy **Start** menü.
* A AdlCopy átmásolja az Azure Data Lakeba. Használati megtekintéséhez írja be a **adlcopy** parancsot.
* A dtui eszköz a felhőben lévő NoSQL-adatbázisba másolja az adatait Azure Cosmos DBba. Adja meg **dtui** parancsot.
* Az Integration Runtime a helyszíni adatforrások és a felhő közötti Adatmásolást végzi. Eszközök, mint például az Azure Data Factory belül használható.
* A Azure PowerShell használatával felügyelheti Azure-erőforrásait a PowerShell programozási nyelvén. Azt is telepítve van a virtuális Gépen.

## <a name="power-bi"></a>Power BI

A DSVM az irányítópultok és a vizualizációk létrehozásához a Power BI Desktop telepítésével jön létre. Az eszköz használható az adatok különböző forrásokból származó az irányítópultokat és jelentéseket készíthet, és közzéteheti őket a felhőbe. További információ: [Power bi-hely](https://powerbi.microsoft.com). Power BI Desktop a **Start** menüben található.

> [!NOTE]
> Egy Power BI eléréséhez a Microsoft Office 365-fiók szükséges.

## <a name="azure-machine-learning-sdk-for-python"></a>A Pythonhoz készült Azure Machine Learning SDK

Az adatszakértők és a mesterséges intelligencia-fejlesztők a Azure Machine Learning SDK for Pythont használják a gépi tanulási munkafolyamatok létrehozásához és futtatásához a [Azure Machine learning szolgáltatással](../service/overview-what-is-azure-ml.md). A szolgáltatást Jupyter jegyzetfüzetekben vagy más Python IDE-ben is használhatja nyílt forráskódú keretrendszerek, például a TensorFlow és a scikit-Learn használatával.

A Python SDK telepítve van a Microsoft Data Science virtuális gépen. A Python SDK használatának megkezdéséhez tekintse meg a [Azure Machine learning használatának első lépései a Python használatával](../service/quickstart-create-workspace-with-python.md)című témakört.

## <a name="more-microsoft-development-tools"></a>További Microsoft-fejlesztői eszközök

A Microsoft [webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx) más Microsoft fejlesztői eszközöket is megtalálhat és tölthet le. Az eszközre a Microsoft Data Science Virtual Machine asztalán is található parancsikon.  

## <a name="important-directories-on-the-virtual-machine"></a>Fontos könyvtárak a virtuális gépen

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



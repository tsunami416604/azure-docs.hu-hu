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
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: b749d8a904bc40eba3346cc03d9274236380c80d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449756"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>A Windows Data Science virtuális gép Azure-beli üzembe helyezése
A Microsoft Data Science virtuális gép egy Windows Azure virtuális gép (VM) rendszerkép előre telepített és konfigurált számos népszerű eszközök, amelyek gyakran használják az adatelemzési és machine learning. Az eszközöket a következők:

* [Az Azure Machine Learning](../service/index.yml) Workbench
* [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda Python elosztási
* Jupyter notebook (az R, Python, PySpark-kernelek)
* A Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Önálló Spark-példány helyi fejlesztési és tesztelési célra
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Gépi tanulási és adatelemzési eszközök
  * Deep Learning-keretrendszerek: beleértve AI-keretrendszerek gazdag választékát [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras részét képezik a virtuális Gépet.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): gyors machine learning-rendszer támogató technikák, például online, kivonatoló, allreduce, csökkentésének, learning2search, aktív, és az interaktív tanulás.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): egy gyorsított fa gyors és pontos végrehajtását biztosító eszköz.
  * [Rattle](http://rattle.togaware.com/) (az R analitikai eszközt, ismerje meg, egyszerűen): olyan eszköz, amely lehetővé teszi az adatok elemzési és gépi tanulási R könnyen az első lépései. Ez magában foglalja a GUI-alapú adatáttekintés és modellezés az R-kód automatikus generálása.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : egy vizuális adatbányászat és a gépi tanulási Java szoftvert.
  * [Apache Drill](https://drill.apache.org/): egy séma nélküli SQL lekérdezési motorja Hadoop, a nosql-alapú és a felhőalapú tárolást.  ODBC, JDBC és felületek engedélyezéséhez lekérdezését NoSQL- és standard szintű BI eszközök, mint például a Power BI-, Excel-, Tableau fájlokat támogatja.
* Az R és Python for kódtárak használata az Azure Machine Learning és más Azure-szolgáltatások
* Többek között a dolgozhat forráskódtárházak, így például GitHub, Visual Studio Team Services Git Bash Git és nyújt számos népszerű Linuxos parancssori segédprogram (beleértve a awk, csökkentésének, perl, a grep, keresés, wget, a curl, stb.) egyaránt a git-bash és a parancs elérhető kérni. 

A feladatok egy sorozatát léptetés adatelemzésre áll:

1. Keresés, betöltés és előzetesen feldolgozni az adatokat
1. Buildelési és tesztelési célokra modellek
1. A modellek intelligens alkalmazásokban felhasználásra üzembe helyezése

Az adatszakértők számos eszközt használja ezeket a feladatokat. Ez elég időigényes lehet kereshet a szoftver, a megfelelő verzióját, majd töltse le és telepítse őket. A Microsoft Data Science virtuális gép is megkönnyítése érdekében ezt a terhet azáltal, hogy egy használatra kész-rendszerképet, amelyek kioszthatóak az Azure-ban minden számos olyan népszerű eszközzel előre telepítve és konfigurálva. 

A Microsoft Data Science virtuális gép jump-starts az elemzési projekt. Ez lehetővé teszi, hogy a különböző nyelveken, köztük a R, Python, SQL és C# feladatokkal. A Visual Studio IDE fejlesztéséhez és teszteléséhez a kódot, amely egyszerűen használható biztosít. Az Azure SDK-t a virtuális gép szerepel lehetővé teszi, hogy az alkalmazások különböző szolgáltatások használatával a Microsoft felhőalapú platformja. 

Nincsenek szoftvereket díjmentesen használható a data science Virtuálisgép-rendszerképet. Csak kell fizetnie az Azure-használati díjak mely a virtuális gép üzembe helyezésekor méretétől függ. A számítási díjak a további részletek találhatók a díjszabás részletei szakaszban a [adatelemző virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) lapot. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Az adatelemző virtuális gép más verziói
Egy [Ubuntu](dsvm-ubuntu-intro.md) lemezkép érhető el, valamint számos hasonló eszközökkel, valamint néhány további mélytanulási keretrendszerekkel. A [CentOS](linux-dsvm-intro.md) rendszerképet is rendelkezésre áll. Emellett egy [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) az adatelemző virtuális gép, azonban néhány eszközök érhetők el csak a Windows Server 2016 rendszeren.  Ez a cikk ellenkező esetben is érvényes a Windows Server 2012 verzióra.

## <a name="prerequisites"></a>Előfeltételek
A Microsoft adatelemző virtuális gép létrehozásához, az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**: beszerzése utólag, lásd: [lekérése az Azure ingyenes próbaverzió](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gép létrehozása
Hozzon létre egy példányt, a Microsoft Data Science virtuális gép, kövesse az alábbi lépéseket:

1. Keresse meg a virtuális gépet, az ajánlati [az Azure portal](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
1. Válassza ki a **létrehozás** gombra az alsó kell figyelembe venni a varázslót.![ Konfigurálja-data-adatelemzési – a virtuális gép](./media/provision-vm/configure-data-science-virtual-machine.png)
1. A varázsló a Microsoft Data Science virtuális gép létrehozásához használt szükséges **bemenetek** minden a **négy lépést** jobb oldalán Ez az ábra számba. Az alábbiakban a bemeneti adatok konfigurálása az egyes lépéseket:
   
   1. **Alapvető beállítások**
      
      1. **Név**: a data science kiszolgáló hoz létre nevet.
      1. **Virtuális merevlemez típusa**: SSD vagy HDD közül választhat. NC_v1 GPU példány (NVidia Tesla K80 alapján), válassza a **HDD** , a lemez típusát. 
      1. **Felhasználónév**: rendszergazdai fiók bejelentkezési azonosítója.
      1. **Jelszó**: rendszergazdai fiók jelszava.
      1. **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.
      1. **Erőforráscsoport**: létrehozhat egy új vagy egy meglévő csoportot.
      1. **Hely**: válassza a leginkább megfelelő adatközpontot. Általában az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a leggyorsabb hálózati hozzáféréshez a fizikai helyéhez legközelebbi.
   1. **Méret**: válassza ki a kiszolgáló típusát, amely megfelel a funkcionális és a költségek megkötések. Virtuálisgép-méretek további lehetőségek "Nézet az összes" gombra kattintva kaphat.
   1. **Beállítások**:
      
      1. **Felügyelt lemezek használata**: felügyelt válassza, ha azt szeretné, hogy a lemezek kezelése a virtuális gép Azure.  Ellenkező esetben kell adjon meg egy új vagy meglévő tárfiókot. 
      1. **Más paramétereket**: általában csak használja az alapértelmezett értékeket. Ha azt szeretné, érdemes megfontolnia a nem alapértelmezett értékek használatát, a kurzort az egyes mezőkkel tájékoztató mutató hivatkozás.
    a. **Összefoglalás**: Győződjön meg arról, hogy minden, a megadott adatok helyesek, és kattintson a **létrehozás**. **Megjegyzés:**: A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretét a számítási túl további díjakat a **mérete** . lépés. 

> [!NOTE]
> A kiépítés körülbelül 10 – 20 percet vesz igénybe. A kiépítési állapota jelenik meg az Azure Portalon.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gép elérése
A virtuális gép létrehozása után azokat a rendszergazdai fiók hitelesítő adatait, amely az előző konfigurálta a távoli asztal is **alapjai** szakaszban. 

A virtuális gép létrehozása és üzembe helyezett, után készen áll az eszközöket, amelyek telepítése és konfigurálása történik rá használatának megkezdéséhez. Start menü csempék és számos olyan eszközzel asztali ikonok vannak. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>A Microsoft Data Science virtuális gépen telepített eszközök



### <a name="microsoft-ml-server-developer-edition"></a>A Microsoft ML Server Developer Edition
Ha szeretné használni a Microsoft nagyvállalati kódtárak méretezhető R vagy Python elemzési, a virtuális gép rendelkezik a Microsoft ML Server Developer edition (korábbi nevén Microsoft R Server) telepítve van. A Microsoft ML Server széles körben üzembe helyezhető, nagyvállalati szintű elemzési platform az R és Python is elérhető, és, üzleti szempontból támogatott biztonságos és skálázható. Egy számos big data típusú statisztikai, prediktív modellezési és gépi tanulási funkciókat támogat, ML Server analytics – feltárása, elemzés, vizualizációs és modellezés teljes skáláját támogatja. Használatával, és kiterjeszti a nyílt forráskódú R és Python, a Microsoft ML Server rendszer teljesen kompatibilis az R / Python parancsfájlok, a functions és a CRAN / pip / Conda-csomagok elemzése a vállalati adatok skálázhatja. Az adatok párhuzamos és darabolásos feldolgozási hozzáadásával nyílt forráskódú R memórián belüli vonatkozó korlátozások is címek. Ez lehetővé teszi, hogy a data analytics sokkal nagyobb, mint a fő memóriában adatfeldolgozással futtassa.  Tartalmazza a virtuális gépen a Visual Studio Community Edition tartalmazza az R Tools a Visual Studio és a Python Tools for Visual Studio-bővítmény, amely egy teljes IDE-R vagy Python használata biztosít. Is biztosítunk más ide-ket is például [RStudio](http://www.rstudio.com) és [PyCharm Community edition](https://www.jetbrains.com/pycharm/) a virtuális gépen. 

### <a name="python"></a>Python
Python fejlesztést az Anaconda Python elosztási 2.7-es és 3.6-os telepítve van. Ehhez a terjesztéshez körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python tartalmazza. Python Tools for Visual Studio (PTVS), amely telepítve van a Visual Studio 2017 Community edition vagy kötegelve IDEs Anaconda TÉTLEN vagy Spyder például az egyik használható. Indíthatja el ezek kereséssel a keresősávba egyikét (**Win** + **S** kulcs).

> [!NOTE]
> Mutasson a Python Tools for Visual Studio, az Anaconda Python 2.7-es, szeretne létrehozni az egyéni környezet minden verzióhoz. Környezet elérési utak be a Visual Studio 2017 Community Edition, navigáljon a **eszközök** -> **Python Tools** -> **Python-környezetek**majd **+ egyéni**. 
> 
> 

Anaconda Python 3.6-os C:\Anaconda van telepítve, és Anaconda Python 2.7-es c:\Anaconda\envs\python2 van telepítve. Lásd: [PVTS dokumentációban](/visualstudio/python/installing-python-interpreters.md) a részletes lépéseket. 

### <a name="jupyter-notebook"></a>Jupyter notebook
Jupyter notebook, megosztani a kódot és az elemzési környezet anaconda terjesztési is tartalmaz. A Jupyter notebook server van előre konfigurálva, a Python 2.7 esetén Python 3.x, Julia és a R PySpark kernelt. Indítsa el a Jupyter-kiszolgálót, és indítsa el a böngészőt a jegyzetfüzet-kiszolgáló eléréséhez "Jupyter Notebook" nevű asztali ikon van. 

A Microsoft van csomagolva több mintafüzetek r és Python A Jupyter notebookok megjelenítése után a Jupyter fér hozzá a Microsoft ML Server, SQL Server Machine Learning Services (adatbázison belüli elemzések), Python, a Microsoft Cognitive ToolKit, tensorflow-hoz és más Azure-technológiák használata. Megjelenik a hivatkozásra kattintva a minták a notebook kezdőlap után hitelesítést a Jupyter notebookot a korábbi lépésben létrehozott jelszó használatával. 

### <a name="visual-studio-2017-community-edition"></a>A Visual Studio 2017 Community edition
A Visual Studio Community edition telepítve van a virtuális gépen. Olyan díjmentes verziója, a népszerű IDE, amelynek tesztelési célra használja, és kis csapatok számára is használhatja. A licencelési feltételeket megtekinthet [Itt](https://www.visualstudio.com/support/legal/mt171547).  Az asztali ikonra duplán kattintva nyissa meg a Visual Studio vagy a **Start** menü. Programok is kereshet **Win** + **S** , és írja be a "Visual Studio". Ha van például a C#, Python, R, node.js nyelven projekteket hozhat létre. Beépülő modulok települnek, amely az Azure-szolgáltatásokhoz hasonlóan az Azure Data Catalog, az Azure HDInsight (Hadoop, Spark) és az Azure Data Lake használata kényelmes. Most már van is egy nevű beépülő modult ```Visual Studio Tools for AI``` , amely zökkenőmentesen integrálható az Azure Machine Learning, és segít gyorsan hozhat létre AI-alkalmazások. 

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet arról, hogy a próbaidőszak lejárt. Adja meg a Microsoft-fiók hitelesítő adatait, vagy hozzon létre egy új ingyenes fiókot a Visual Studio Community Edition eléréséhez. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
A virtuális gépen, az R vagy Python biztosítja egy ML szolgáltatásokkal futtatni az adatbázison belüli elemzések SQL Server 2017 developer verziója. Machine Learning-szolgáltatások fejlesztéséhez és üzembe helyezéséhez intelligens alkalmazások platformot biztosítani. Használhatja a sokoldalú, hatékony ezeken a nyelveken és csomagok számát, a közösségi modelleket hozhat létre és előrejelzések készítése az SQL Server-adatok. Elemzési adatokhoz közel képes tartani, mivel a Machine Learning Services (adatbázison belüli) integrálható belül az SQL Server az R- és Python nyelven. Ezzel elkerülhető a költségek és az adatáthelyezés biztonsági kockázatokat.

> [!NOTE]
> Az SQL Server developer Edition verziót csak fejlesztési és tesztelési célokra használható. Éles környezetben futtatásához licenc szükséges. 
> 
> 

Érhető el az SQL server indítása **SQL Server Management Studio**. A virtuális gép neve megjelenik a kiszolgáló neveként. Ha a Windows rendszergazdaként bejelentkezve Windows-hitelesítést használjon. Miután az SQL Server Management Studio más felhasználók létrehozása, adatbázisok létrehozása, adatok importálása, és futtasson SQL-lekérdezéseket. 

Ahhoz, hogy az adatbázison belüli elemzések SQL Machine Learning Services segítségével, futtassa a következő parancsot, egy egyszeri művelet az SQL Server management studióban a kiszolgálói rendszergazdai bejelentkezés után. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Több Azure-eszközök telepítve vannak a virtuális gépen:

* Nincs az Azure SDK-dokumentáció eléréséhez egy asztali parancsikonjára. 
* **Az AzCopy**:-ból a Microsoft Azure Storage-fiók adatok mozgatására szolgál. Használati megtekintéséhez írja be a **Azcopy** , a használati parancsot a parancssorba. 
* **A Microsoft Azure Storage Explorer**: keresse meg az Azure storage szolgáltatásba vagy onnan az Azure Storage-fiókot és az átviteli adatok tárolt objektumok között használt. Beírhatja **Storage Explorer** lévő keresése, vagy keresse meg azt a Windows Start menü az eszköz eléréséhez. 
* **Az Adlcopy**: adatok áthelyezése az Azure Data Lake segítségével. Használati megtekintéséhez írja be a **adlcopy** parancsot. 
* **dtui**: adatok importálására és az Azure Cosmos DB egy NoSQL-adatbázis, a felhő segítségével. Típus **dtui** parancssorban. 
* **Az Azure Data Factory integrációs modul**: lehetővé teszi az adatok áthelyezését a helyszíni adatforrások és a felhő között. Eszközök, mint például az Azure Data Factory belül használható. 
* **A Microsoft Azure Powershell**: a Powershell parancsnyelv is telepítve van a virtuális gépen az Azure-erőforrások felügyeletére szolgáló eszköz. 

### <a name="power-bi"></a>Power BI
Irányítópultok és nagyszerű Vizualizációk segíteni a **Power BI Desktop** telepítve van. Az eszköz használható az adatok különböző forrásokból származó az irányítópultokat és jelentéseket készíthet, és közzéteheti őket a felhőbe. További információ: a [Power BI](http://powerbi.microsoft.com) hely. A Start menüben található Power BI desktopban. 

> [!NOTE]
> Office 365-fiókja eléréséhez a Power bi-ban van szüksége. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Az Azure Machine Learning Workbench egy asztali alkalmazás és parancssori felület. A Workbench rendelkezik beépített adat-előkészítés, amely megtanulja az adat-előkészítési lépéseket azok végrehajtása. Is biztosít a projektmenedzsment, a futtatási előzmények vagy a jegyzetfüzet-integráció tevékenységekbe, így növelheti a hatékonyságot. A legjobb nyílt forráskódú keretrendszerekkel, beleértve a TensorFlow, a Cognitive Toolkit, a Spark Machine Learning és a scikit előnyeit is – ismerje meg, hogyan fejleszthet a modellek. A dsvm-hez biztosítunk egy asztali ikonra az Azure Machine Learning workbench telepítése a felhasználó % LOCALAPPDATA % könyvtárba. Minden egyes felhasználó esetében kell használnia a Workbench szüksége van egy egyszeri művelet, kattintson duplán a ```AzureML Workbench Setup``` asztali ikonra a Workbench a példányának a telepítéséhez. Az Azure Machine Learning is hoz létre, és használja a felhasználónkénti Python-környezetet, amely a % LOCALAPPDATA%\amlworkbench\python ki kell olvasni.

## <a name="additional-microsoft-development-tools"></a>További Microsoft fejlesztési eszközök
A [ **Microsoft Webplatform-telepítő** ](https://www.microsoft.com/web/downloads/platform.aspx) felderítése és más Microsoft fejlesztői eszközöket tölthet le is használható. Emellett van egy parancsikont a Microsoft adatelemző virtuális gép asztalán a megadott eszköz.  

## <a name="important-directories-on-the-vm"></a>A virtuális gép fontos könyvtárak
| Elem | Címtár |
| --- | --- |
| Jupyter notebook server konfigurációk |C:\ProgramData\jupyter |
| Jupyter Notebook minták kezdőkönyvtár |c:\dsvm\notebooks és c:\users\<username > \notebooks|
| Más minták |c:\dsvm\samples |
| Anaconda (alapértelmezett: Python 3.6-os) |c:\Anaconda |
| Anaconda Python 2.7-es környezetben |c:\Anaconda\envs\python2 |
| A Microsoft ML Server önálló Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Az R alapértelmezett példányt (gépi tanulás kiszolgáló önálló) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| Machine Learning-szolgáltatások az SQL adatbázis-példány könyvtár |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Az Azure Machine Learning Workbench (felhasználónként) | %localappdata%\amlworkbench | 
| Egyéb eszközök |c:\dsvm\tools |

> [!NOTE]
> A dsvm-hez és Windows Server 2016 Edition 2018 március előtti Windows Server 2012 kiadásán alapértelmezett Anaconda környezete Python 2.7-t. A másodlagos környezete Python 3.5-ös c:\Anaconda\envs\py35 helyen található. 
> 
> 

## <a name="next-steps"></a>További lépések
Az alábbiakban további lépések a tanulási és feltárása a folytatáshoz. 

* Megismerheti azokat a különféle beépített adatelemzési eszközzel az adatelemző virtuális gép ehhez kattintson a start menüben, és az eszközök kivételkor látható a menüben.
* Ismerje meg a termék ellátogat az Azure Machine Learning-szolgáltatások és a Workbench [rövid és oktatóanyagok lap](../service/index.yml). 
* Navigáljon a **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** minták használatával a RevoScaleR, amely támogatja a data analytics nagyvállalati szintű R-erőforrástárat.  
* Olvassa el a cikket: [10 dolog, amire alkalmas az adatelemző virtuális gép](http://aka.ms/dsvmtenthings)
* Ismerje meg, hogyan rendszeresen használatával teljes körű elemzési megoldásokat hozhat létre a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml).
* Látogasson el a [Azure AI-katalógusban](http://gallery.cortanaintelligence.com) machine learning és a data analytics minták Azure Machine learning és a kapcsolódó adatokat használó szolgáltatások az Azure-ban. Egy ikont is biztosítunk az a **Start** menü, és ez a katalógus a virtuális gép asztalán.


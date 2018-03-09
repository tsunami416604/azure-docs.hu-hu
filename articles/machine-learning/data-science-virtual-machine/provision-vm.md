---
title: "A Windows Data tudományos Azure virtuális gép kiépítéséhez |} Microsoft Docs"
description: "Konfigurálja és tudományos adatok virtuális gép létrehozása az Azure elemzéséhez és a gépi tanulás."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 9f01ba69f6511a3f9a7f99e379522be3c00554f5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>A Windows Data tudományos virtuális gépet az Azure telepítéséhez
A Microsoft adatokat tudományos virtuális gép, a Windows Azure virtuális gép (VM) előtelepített és konfigurált számos népszerű eszköz adatelemzés és a gépi tanulás általánosan használt lemezkép. A rendszer részét képező eszközök:

* [Az Azure gépi tanulás](../preview/index.yml) munkaterület
* [Microsoft gépi tanulási a kiszolgáló](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda Python elosztási
* Jupyter notebook (az R, Python, PySpark mag)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Önálló Spark-példány a helyi fejlesztéshez és teszteléshez
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Gépi tanulás és adatelemzés eszközök
  * A részletes tanulási keretrendszerek: széles választéka AI keretrendszerek, beleértve a [Microsoft kognitív eszközkészlet](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras részét képezik a virtuális Gépet.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): gyors machine learning-rendszer támogatása, például a online, a kivonatoló, allreduce, csökkentése, learning2search, aktív, és interaktív tanulási.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): gyors és pontos súlyozott fa megvalósítási biztosító eszközt.
  * [Rattle](http://rattle.togaware.com/) (az R analitikai eszköz a további könnyen): olyan eszköz, amely lehetővé teszi a adatelemzés és gépi tanulási R könnyen az első lépések. Tartalmazza a grafikus felhasználói felületen alapuló adatok feltárása és az R-kód automatikus generálása modellezési.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : A látványelemek adatainak adatbányászati és gépi tanulási szoftver Java nyelven.
  * [Apache részletezési](https://drill.apache.org/): sémamentes SQL lekérdezési motorja Hadoop, a nosql-alapú és a felhőalapú tárolást.  ODBC és JDBC felületek engedélyezése lekérdező nosql-alapú és a szabványos Üzletiintelligencia-eszközök, például a Power BI, az Excel, a Tableau fájlokat támogatja.
* Az R és Python a szalagtárak használja az Azure Machine Learning és más Azure-szolgáltatásokkal
* Többek között beleértve a github webhelyen, a Visual Studio Team Services forráskódú adattárakban dolgozni a Git Bash Git számos népszerű Linux parancssori segédprogram (beleértve a awk, csökkentésének, perl, grep, keresés, wget, curl, stb.), és elérhető, mind a git bash-eszközt, illetve figyelmeztetés. 

Adattudomány Ez magában foglalja a feladatok sorozata léptetés:

1. Keresése, betöltése, és előzetesen feldolgozni az adatokat
2. Összeállításakor és tesztelésekor modellek
3. A modelleket a fogyasztás intelligens alkalmazások telepítése

Adatszakértőkön számos különféle eszközre segítségével ezeket a feladatokat. Túl sok időt vesz igénybe találja a szoftver, a megfelelő verzióját, majd töltse le és telepítse őket a lehet. A Microsoft Data tudományos virtuális gép a terheket megkönnyítheti azzal, hogy biztosít egy használatra kész lemezképnek, amely telepíthető az Azure összes számos népszerű eszköz előtelepített és konfigurált. 

A Microsoft Data tudományos virtuális gép jump-starts analytics projektjéhez. Ez lehetővé teszi, hogy működik a feladatok R, Python, SQL és C# például különböző nyelveken. A Visual Studio biztosít egy IDE fejlesztéséhez és teszteléséhez a kódot, amely könnyen használható. Az Azure SDK tartalmazza a virtuális Gépet a különböző szolgáltatások segítségével a Microsoft felhőalapú platformja alkalmazások létrehozását teszi lehetővé. 

Nincsenek az adatok tudományos VM-lemezkép szoftver költségek. Csak kell fizetnie Azure használati díjak mely a virtuális gép kiépítése méretétől függ. További részleteket a számítási díjakat található az árazás részletes adatait tartalmazó részben található meg a [adatok tudományos virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) lap. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Az adatok tudományos virtuális gép egyéb verziói
Egy [Ubuntu](dsvm-ubuntu-intro.md) kép is, amelyek számos hasonló eszközök és néhány további mély keretrendszerek tanulási érhető el. A [CentOS](linux-dsvm-intro.md) kép is érhető el. Is kínál a [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) az adatok tudományos virtuális gép, ha néhány eszközök áll csak a Windows Server 2016-os verziójában elérhető.  Ez a cikk ellenkező esetben is érvényes a Windows Server 2012 verzióra.

## <a name="prerequisites"></a>Előfeltételek
A Microsoft Data tudományos virtuális gép létrehozásához, az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**: egy beszerzéséről [beolvasása az Azure ingyenes próbaverzió](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>A Microsoft Data tudományos virtuális gép létrehozása
A Microsoft Data tudományos virtuális gép példány létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg a virtuális gépet, a listaelem [Azure-portálon](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Válassza ki a **létrehozása** panel alján, a varázsló veendő.![ Konfigurálja-adatok-tudományos-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. A varázsló a Microsoft Data tudományos virtuális gép létrehozásához használt szükséges **bemenetek** az egyes a **négy lépésben** a jobb oldali ábra számba. Az alábbiakban az egyes lépéseket konfigurálásához szükséges adatokat:
   
   1. **Alapvető beállítások**
      
      1. **Név**: az adatok tudományos kiszolgáló létrehozásakor nevét.
      2. **Virtuális gép lemeztípus**: SSD és HDD választhat. NC_v1 GPU példány (NVidia Tesla K80 alapján), válassza a **HDD** , a lemez típusát. 
      3. **Felhasználónév**: rendszergazdai fiók bejelentkezési azonosító.
      4. **Jelszó**: rendszergazdai fiók jelszavát.
      5. **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki a amelyiken a gép létrehozását és számlázva van.
      6. **Erőforráscsoport**: hozhat létre egy új vagy meglévő csoport használata.
      7. **Hely**: válassza ki a legjobban megfelelő adatközpont. Általában az adatközpont, amely az adatok, vagy a helynév leggyorsabb hálózati hozzáférési legközelebb.
   2. **Méret**: válassza ki a kiszolgáló típusát, amely megfelel a funkcionális és költség megkötések. További lehetőségek a VM-méretek kaphat kiválasztása a "Nézet All".
   3. **Beállítások**:
      
      1. **Felügyelt lemezeket használó**: felügyelt válassza, ha azt szeretné, hogy a lemezek kezelése a virtuális gép Azure.  Ellenkező esetben meg kell adnia egy új vagy meglévő tárfiókot. 
      2. **Más paraméterek**: általában csak használja az alapértelmezett értékeket. Ha azt szeretné, figyelembe kell venni a nem alapértelmezett értékeket használ, mutasson az egyes mezőkkel tájékoztató mutató hivatkozást.
    a. **Összefoglalás**: Győződjön meg arról, hogy az összes megadott adatok helyesek, és kattintson a **létrehozása**. **Megjegyzés:**: A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretéhez számítási túl további díjakat a **mérete** lépés. 

> [!NOTE]
> A kiépítése körülbelül 10-20 percet kell végrehajtani. A kiépítési állapotát az Azure portálon jelenik meg.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Hogyan érhetők el a Microsoft Data tudományos virtuális gép
A virtuális gép létrehozása után azokat a rendszergazdai fiók hitelesítő adatait az előző konfigurált használatával is a távoli asztal **alapjai** szakasz. 

Miután a virtuális gép létrehozása és üzembe helyezve, készen áll indíthatja azon eszközöket, amelyek telepítése és konfigurálása történik meg. Start menü csempék és számos eszközt az asztali ikonok vannak. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Eszközök vannak telepítve a Microsoft Data tudományos virtuális gép



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Ha szeretné használni a Microsoft vállalati szalagtárak méretezhető R vagy Python az elemzésekhez, a virtuális Gépnek legyen telepítve (korábbi nevén Microsoft R Server) Microsoft ML Server Developer kiadásában. Microsoft ML Server egy körben telepíthető vállalati szintű analytics platform R és Python is elérhető, és méretezhető, minden üzleti szempontból támogatott és biztonságos. Big Data típusú adatok statisztikák, a prediktív modellezési és a gépi tanulási képességek számos támogató, ML-kiszolgáló összes analytics – feltárása, elemzés, adatmegjelenítési és modellezési támogatja. Használatával, és nyílt forráskódú R és Python, Microsoft ML Server rendszer teljesen kompatibilis, az R vagy Python parancsfájlok, funkciók és CRAN / pip / Conda csomagokat, a vállalati adatok elemzésére méretezése. Adja hozzá az adatok párhuzamos és darabolt feldolgozása nyitott forrás R memórián belüli vonatkozó korlátozások is javítja. Ez lehetővé teszi, hogy futhat az analytics adatok nagyobb, mint mi elfér a fizikai memóriát.  A Visual Studio Community Edition szerepel-e a virtuális gép tartalmazza a Visual Studio és a Python eszközök Visual Studio-bővítmény, amely egy teljes IDE biztosít R vagy Python R eszközöket. Is biztosítunk más IDEs is, mint [Rstudióból](http://www.rstudio.com) és [PyCharm Community edition](https://www.jetbrains.com/pycharm/) a virtuális Gépen. 

### <a name="python"></a>Python
A fejlesztési pythonos környezetekben Anaconda Python elosztási 2.7 és 3.6 telepítve van. Ehhez a terjesztéshez együtt a legnépszerűbb matematikai, tervezés és adatok analytics csomagok körülbelül 300 alap Python tartalmazza. Python Tools használhatja a Visual Studio (PTVS) belül a Visual Studio 2017 Community edition vagy kötegelt IDEs Anaconda ÜRESJÁRATBAN vagy Spyder például az egyik telepített. Indítja el az egyik meg a keresési sávon rákeresve (**Win** + **S** kulcs).

> [!NOTE]
> A Python Tools for Visual Studio Anaconda Python 2.7, mutasson létrehozásához szükséges egyéni környezetek egyes verzióihoz. A Visual Studio 2017 Community Edition környezet elérési utak beállításához navigáljon **eszközök** -> **Python Tools** -> **Python-környezetek**majd **+ egyéni**. 
> 
> 

Anaconda Python 3.6 telepítőmappájában található C:\Anaconda és Anaconda Python 2.7 c:\Anaconda\envs\python2 telepítőmappájában található. Lásd: [PVTS dokumentációban](/visualstudio/python/python-environments.md#selecting-and-installing-python-interpreters) a részletes lépéseket. 

### <a name="jupyter-notebook"></a>Jupyter notebook
Jupyter notebook, egy környezet kóddal és elemzési anaconda terjesztési is tartalmaz. A Jupyter notebook kiszolgáló már előre konfigurált, a Python 2.7, Python 3.x, PySpark, Ágnes és R kernelek. Nincs a Jupyter kiszolgáló indítása, és indítsa el a böngészőt a Notebook kiszolgáló eléréséhez "Jupyter Notebook" nevű asztali ikon. 

A Microsoft több minta jegyzetfüzetet r és Python csomagolását A Jupyter notebookok megjelenítése után hozzáférhet a Jupyter Microsoft ML Server, SQL Server ML Services (adatbázis-analytics), Python, Microsoft kognitív eszközkészlet, Tensorflow és más Azure technológiák használata. A notebook kezdőlapján a hivatkozásra kattintva a minták után a hitelesítést a Jupyter notebook a korábbi lépésben létrehozott jelszó használatával tekintheti meg. 

### <a name="visual-studio-2017-community-edition"></a>A Visual Studio 2017 Community edition
A Visual Studio Community edition telepítve a virtuális Gépen. A népszerű IDE kis csoportjai és tesztelési célokra használható Microsoft ingyenes verzióját is. A licencfeltételek megtekintheti [Itt](https://www.visualstudio.com/support/legal/mt171547).  Az asztali ikonra duplán kattintva nyissa meg a Visual Studio vagy a **Start** menü. Programokat is kereshet **Win** + **S** , majd gépelje be a "Visual Studio". Ha van például a C#, Python, R, node.js nyelvű projektek is létrehozhat. Beépülő modulok települnek, amely együttműködik az Azure-szolgáltatásokat, mint az Azure Data Catalog, az Azure HDInsight (Hadoop, Spark) és az Azure Data Lake kényelmes. Most nincs is nevezett beépülő modul ```Visual Studio Tools for AI``` , amely zökkenőmentesen integrálható az Azure Machine Learning és a segítségével gyorsan build AI alkalmazások. 

> [!NOTE]
> Üzenet jelenik meg, hogy a próbaidőszak lejárt kaphat. Microsoft-fiók hitelesítő adatait, vagy hozzon létre egy új ingyenes fiókot, a Visual Studio Community Edition elérésének. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
Egy SQL Server 2017 ML Services adatbázis-analytics futtatásához fejlesztői verzióját a virtuális Gépen az R vagy Python valósul meg. Gépi tanulás biztosítanak a platform fejlesztéséhez és intelligens alkalmazások telepítéséhez. Használhatja a hatékony és erőteljes ezeken a nyelveken és a Közösség számos csomag modellek létrehozása, és előrejelzés létrehozásához az SQL Server-adatok. Az adatok közel analytics mert ML-szolgáltatások (az adatbázis-) integrálja az R és Python nyelvű SQL-kiszolgálón belül tárolhatja. Ezzel a megoldással a költségek és az adatátvitelt jelölik a kapcsolódó biztonsági kockázatokról.

> [!NOTE]
> Az SQL Server developer kiadásában csak fejlesztési és tesztelési célokra használható. Licenccel kell rendelkeznie a termelési futtatásához. 
> 
> 

Az SQL server férhetnek megnyitása **SQL Server Management Studio**. A virtuális gép neve van feltöltve, a kiszolgáló neve. Ha a Windows rendszergazdaként jelentkezett be Windows-hitelesítés használatára. Miután az SQL Server Management Studio más felhasználók létrehozása, adatbázisok létrehozására, importálhat adatokat, és az SQL-lekérdezések futtatása. 

Adatbázis-analytics SQL ML-szolgáltatások használatának engedélyezéséhez futtassa a következő parancsot ütemezésként idő a kiszolgáló-rendszergazdai bejelentkezés után az SQL Server management studio művelet. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Több Azure-eszközök telepítve vannak a virtuális Gépet:

* Nincs az Azure SDK-dokumentáció eléréséhez asztali parancsikonjára. 
* **AzCopy**: áthelyezni az adatokat mindkét a Microsoft Azure Storage-fiókot használni. Használat megtekintéséhez írja be **Azcopy** parancsot a parancssorba a használat megtekintéséhez. 
* **A Microsoft Azure Tártallózó**: az Azure Storage-fiókot és átviteli adatokat az Azure storage érkező vagy oda irányuló tárolt objektumok között használt. Beírhatja **Tártallózó** a Keresés vagy nem található, a Windows Start menüben, az eszköz eléréséhez. 
* **Adlcopy**: adatok áthelyezése az Azure Data Lake használt. Használat megtekintéséhez írja be **adlcopy** parancssorban. 
* **dtui**: áthelyezni az adatokat, és az Azure Cosmos DB, egy NoSQL-adatbázis, a felhő használt. Típus **dtui** a parancssorból. 
* **Az Azure Data Factory integrációs futásidejű**: lehetővé teszi, hogy a helyszíni adatforrások és a felhő közötti adatátvitelt jelölik. Például az Azure Data Factory belül használják. 
* **A Microsoft Azure Powershell**: a Powershell programozási nyelv is telepítve van a virtuális Gépet az Azure-erőforrások felügyeletéhez használt eszköz. 

### <a name="power-bi"></a>Power BI
Irányítópultok és a kiváló megjelenítések létrehozásához a **Power BI Desktop** telepítve van. Az eszköz segítségével szerez adatokat különböző forrásokból származó az irányítópultokat és jelentéseket készít, és közzéteheti a felhőben. További információ: a [Power BI](http://powerbi.microsoft.com) hely. A Start menüben található Power BI desktopban. 

> [!NOTE]
> Power BI eléréséhez az Office 365-fiók szükséges. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Az Azure Machine Learning-munkaterület egy asztali alkalmazás és a parancssori felület. A munkaterület rendelkezik beépített adatok előkészítése, amely az adatok előkészítő lépések megtanulja őket hajt végre. Project management, futtassa az előzményeket és a termelékenység fokozni notebook integrációs is tartalmazza. Kihasználhatja a legjobb nyílt forráskódú keretrendszerekre, beleértve a TensorFlow, kognitív eszközkészlet, Spark ML és scikit – ismerje meg, a modellek fejlesztéséhez. A DSVM nyújtunk telepítése az Azure Machine Learning-munkaterület könyvtárba, amely a felhasználó % LOCALAPPDATA % asztali ikon. Minden olyan felhasználóhoz, használja a munkaterületet üzemeltető kell egy time művelet dupla kattintás a ```AzureML Workbench Setup``` azok a munkaterület-példány telepítését asztali ikonra. Az Azure Machine Learning is hoz létre, és használja ki kell olvasni a % LOCALAPPDATA%\amlworkbench\python felhasználói Python-környezetben.

## <a name="additional-microsoft-development-tools"></a>További Microsoft fejlesztőeszközök
A [ **Microsoft Webplatform-telepítő** ](https://www.microsoft.com/web/downloads/platform.aspx) felderítése és más Microsoft fejlesztői eszközök is használható. Az eszköz a Microsoft Data tudományos virtuális gép asztalán mutató hivatkozás is van.  

## <a name="important-directories-on-the-vm"></a>A virtuális gép fontos könyvtárak létrehozása sikerült
| Elem | Címtár |
| --- | --- |
| Jupyter notebook kiszolgáló konfigurációk |C:\ProgramData\jupyter |
| Jupyter Notebook minták kezdőkönyvtár |c:\dsvm\notebooks és c:\users\<felhasználónév > \notebooks|
| Más minták |c:\dsvm\samples |
| Anaconda (alapértelmezett: Python 3.6) |c:\Anaconda |
| Anaconda Python 2.7 environment |c:\Anaconda\envs\python2 |
| Microsoft ML Server Standalone Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Alapértelmezett R-példány (ML-kiszolgáló önálló) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL ML szolgáltatások adatbázis-példány könyvtára |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Az Azure Machine Learning munkaterület (felhasználónként) | %localappdata%\amlworkbench | 
| Egyéb eszközök |c:\dsvm\tools |

> [!NOTE]
> A Windows Server 2012 edition DSVM és a Windows Server 2016. március 2018 előtt kiadás az alapértelmezett Anaconda környezete Python 2.7. A másodlagos környezete Python 3.5 c:\Anaconda\envs\py35 helyen. 
> 
> 

## <a name="next-steps"></a>További lépések
Az alábbiakban a lépéseket, ahol folytathatja a tanulási és kutatási funkciójával. 

* Megismerkedhet a különböző adatok tudományos eszközök az adatok tudományos virtuális gép által a start menü és az a menü felsorolt eszközök.
* További tudnivalók Azure Machine Learning szolgáltatás és a munkaterületet üzemeltető érhetők el a termék [gyors üzembe helyezés és oktatóanyagok lap](../preview/index.yml). 
* Navigáljon a **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** mintákat az R, amely támogatja a vállalati léptékű adatelemzés RevoScaleR szalagtár használatával.  
* A következő cikkben: [10 lehetősége van az adatok tudományos virtuális gép](http://aka.ms/dsvmtenthings)
* Ismerje meg, hogyan hozhat létre a teljes körű elemzési megoldásokat rendszeresen használatával a [Team adatok tudományos folyamat](../team-data-science-process/index.yml).
* Látogasson el a [Azure Eszközintelligencia-katalógus](http://gallery.cortanaintelligence.com) Azure machine learning és adatok analytics minták Azure Machine learning és a kapcsolódó adatokat használó szolgáltatási. Egy ikont is adtunk meg a **Start** menü és a virtuális gépet a tár az asztalon.


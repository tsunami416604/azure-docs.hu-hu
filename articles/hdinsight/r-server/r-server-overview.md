---
title: Az R Server on Azure HDInsight bemutatása |} Microsoft Docs
description: Megtudhatja, hogyan R Server használata a HDInsight a big data elemző alkalmazások létrehozásához.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19334e78124d1e388bc760659385388d89953644
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>R Server, és nyílt forráskódú R képességek a HDInsight bemutatása

Microsoft R Server (Microsoft Machine Learning-kiszolgálóként is tudja) az Azure-ban létrehozhat HDInsight-fürtök esetén érhető el a központi telepítési beállításaként. A fürt, amely ezt a lehetőséget biztosít neve **R Server**. Ezt a képességet biztosít a adatelemzők, statisztikusok és R-t használó programozók méretezhető, igény szerinti hozzáféréssel rendelkező elosztott elemzés hdinsight platformon történő módszerek.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Az R Server on HDInsight a legújabb funkciókat az R-alapú használatával szinte bármilyen méretű, vagy az Azure Blob, vagy a Data Lake tárolási betöltött adatkészletek biztosít. R Server-fürt nyílt forráskódú R épül, mivel az R-alapú alkalmazások létrehozása kihasználhatják a 8000 + nyílt forráskódú R csomagok bármelyikét. A feladatok ScaleR, a Microsoft big data elemzés csomag részét képező R Server, az is elérhetők.

A fürt élcsomópont kényelmes csatlakozzon a fürthöz, és az R parancsfájlok futtatásához. Egy élcsomópontot az informatikai részleg parallelized elosztott feladatai ScaleR a peremhálózati kiszolgáló a magokon futtassák. Is futtathatja őket a fürt csomópontjai között ScaleR a Hadoop térkép csökkentése használatával, vagy Spark számítási környezeteket.

A modellek vagy előrejelzéseket elemzés a helyi használatra tölthető le. Akkor is is lehet operationalized máshol Azure, különösen keresztül [Azure Machine Learning Studio](http://studio.azureml.net) [webszolgáltatás](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-server-on-hdinsight"></a>A HDInsighton futó R Server használatának első lépései

Az R Server-fürt létrehozása az Azure HDInsight, válassza ki a **R Server** fürt típusa, az Azure portál használatával HDInsight-fürtök létrehozásakor. Az R Server-fürt típusa tartalmaz R Server, az adatok a fürt csomópontjai és egy élcsomópontot, az R Server-alapú analytics követően zónának szolgál. Lásd: [Ismerkedés az R Server on HDInsight az](r-server-get-started.md) a fürt létrehozásához útmutatást.

## <a name="why-choose-r-server-in-hdinsight"></a>Miért válassza ki az R Server a hdinsight eszközben?

R Server a HDInsight lehetővé teszi az R Server használja az Azure-on. R Server tartalmazza:

+ **A legjobb AI innováció a Microsoft és nyílt forráskódú**

  Microsoft nagy hangsúlyt fektet AI minden egyedi és a szervezet számára tegye. R Server jól skálázható, elosztott algoritmusok készleteiből állnak gazdag készletét tartalmazza, mint [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), és [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , amely nagyobb méretű adatokkal dolgozhat fizikai memória, és számos különböző platformokról elosztott módon futtassa a mérete. Ismerje meg, további információk a Microsoft a gyűjteményhez tartozó egyéni [R csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) és [Python-csomagokat](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a termékhez mellékelt.
  
  R Server kulcsösszetevő ezek Microsoft újításait és azok érkező összes fölött egy egyetlen vállalati szintű platform nyílt forráskódú közösségi (R, Python és AI eszközök gazdag). Bármely R vagy Python nyílt forráskódú gépi tanulás csomag egymás melletti együttműködik a Microsoft semmilyen szellemi tulajdont képező innováció. 

+ **Egyszerű, biztonságos és nagy méretű operationalization és felügyelet**

  A vállalatok számára a hagyományos operationalization mintáknak és környezetekben függő végül befektetés sok időt és fáradozik annak érdekében, hogy ez a terület. Problémás pontok gyakran felfújt költségeket eredményez, és késéseket tartalmazzák: a fordítási idő modellekhez, hogy továbbra is érvényes, és a jelenlegi, az ismétlések szabályozó jóváhagyási engedélyekkel a operationalization kezelése.

  R Server nyújtja a legjobb minőségű [operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) --a óta, a gépi tanulási modell befejeződött, néhány kattintással webszolgáltatások API-k létrehozásához szükséges. Ezek [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) server rács üzemelteti a helyszíni vagy a felhőben és üzleti alkalmazásokkal integrálható. Központi telepítése egy vállalati, mind a kötegelt és a valós idejű pontozási igényekkel zökkenőmentesen méretezheti rugalmas rács lehetővé teszi. Útmutatásért lásd: [azok R Server on HDInsight](r-server-operationalize.md).

+ **A részletes ökoszisztéma kapcsolattartás során képes biztosítani az ügyfél sikeres optimális teljes tulajdonosi költség, a**

  Egyéni felhasználók számára, így az alkalmazások intelligens vagy egyszerűen kívánó ismerje meg az új világ AI és gépi tanulási kell a megfelelő erőforrásokat segítséget nyújthatnak a kezdéshez az út az alkalmazná. Ebben a dokumentációban mellett Microsoft több képzési erőforrást tartalmaz, és több képzési partnerek hatékonyságának növeléséhez, és gyorsan eredményesebbé folytat.


## <a name="key-features-of-r-server-on-hdinsight"></a>Az R Server on HDInsight a kulcsfontosságú szolgáltatásokat

Az R Server on HDInsight az alábbi szolgáltatásokat tartalmazza.

| A szolgáltatás kategória | Leírás |
|------------------|-------------|
| R-engedélyezve | [R csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) megoldások írt R, egy nyílt forráskódú eloszláshoz R és futásidejű infrastruktúra parancsfájl végrehajtására. |
| Python-engedélyezve | [Python-modulok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a Python és a parancsfájl végrehajtása a futtatási infrastruktúra egy nyílt forráskódú eloszláshoz, pythonban írt megoldások.  
| [Előre betanított modellek](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | A vizuális elemzés és a szöveg véleményeket elemzésekhez készen áll az adatok pontozása megadnia. |
| [Központi telepítése és felhasználása](r-server-operationalize.md) | Azok a kiszolgáló, és állíthat rendszerbe megoldásokat webszolgáltatásként. |
| [Távoli végrehajtás](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | R-kiszolgálón működő távoli munkamenetek indításához ügyfél a munkaállomáson a hálózaton. |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>A HDInsight R Server adatok tárolási lehetőségek

A HDInsight-fürtök a HDFS fájlrendszerében alapértelmezett tároló vagy egy Azure Storage-fiókot, vagy egy Azure Data Lake Store társítható. Ez a társítás biztosítja, hogy az adatokat tölt fel a fürt, tárolás elemzési során állandó történik, és a fürtök törlése után is érhető el az adatokat. Számos különböző eszköz az adatok átvitele a tárolási lehetőség választ ki, beleértve a feltöltési portálalapú létesítmény a tárfiók kezelése és a [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogramot.

Lehetősége van további Blob való hozzáférés hozzáadása, és a Data lake tárolja a kiépítési folyamat függetlenül használja az elsődleges tárolási lehetőség fürt során. Lásd: [Ismerkedés az R Server on HDInsight az](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) további fiókokat ad hozzá hozzáférési olvashat. Tekintse meg a kiegészítő [Azure tárolási lehetőségek R Server on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) több storage-fiókok használatával kapcsolatos további olvashatja.

Is [Azure fájlok](../../storage/files/storage-how-to-use-files-linux.md) tárolási beállításként való használatra a peremhálózati csomóponton. Az Azure Files lehetővé teszi a fájlmegosztást, amelyet a Linux fájlrendszerben létrejött az Azure Storage csatlakoztatni. HDInsight-fürt az R Serverhez adatok tárolási lehetőségek kapcsolatos további információkért lásd: [Azure tárolási lehetőségek R Server on HDInsight](r-server-storage.md).

## <a name="access-machine-learning-server-on-the-cluster"></a>Machine Learning kiszolgálót a fürtön

Csatlakozhat a Microsoft Machine Learning Server, a peremhálózati csomóponton böngésző használatával. Telepíti a rendszer alapértelmezés szerint a fürt létrehozása során. További információkért lásd: [az R Server on HDInsight az beszerzése stared](r-server-get-started.md). Keresztül is csatlakozhat az ML-kiszolgáló a parancssorból az R-konzol eléréséhez a PuTTY SSH/használatával. 

## <a name="develop-and-run-r-scripts"></a>Fejlesztéséhez és az R parancsfájlok futtatása

Az R parancsfájlok létrehozása és futtatása használhatja a 8000 + nyílt forráskódú R csomagok elérhető párhuzamos működésű és elosztott rutinok mellett a ScaleR könyvtárban. R Server élcsomópont a futó parancsfájl általában az R parancsértelmező belül fut, ezen a csomóponton. A kivételek közé tartoznak a fenti lépések, amelyeket a beállított számítási környezetet ScaleR függvény Hadoop térkép csökkentése (RxHadoopMR) vagy a Spark (RxSpark). A függvény ebben az esetben egy elosztott módon fut, ezen adatok (feladat) csomópontokon a fürt társított hivatkozott adatokkal. A különböző számítási környezet beállításokkal kapcsolatos további információkért lásd: [számítási környezet lehetőségek R Server on HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Modell üzembe helyezése

Az adatok modellezési befejeződése után az új adatokat, vagy az Azure és a helyszíni előrejelzéseket készítsen a modellt üzembe. Ez a folyamat pontozási néven ismert. Pontozó teheti HDInsight, Azure Machine Learning vagy a helyszínen.

### <a name="score-in-hdinsight"></a>Pontszám a Hdinsightban
Pontozásához a hdinsight eszközben, az R függvényt, amely meghívja a modell a tárfiók betöltött új adatfájl előrejelzéseket készítsen írni. Ezután menti az előrejelzés a tárfiók. A szokásos igény szerinti élcsomópont a fürt vagy egy ütemezett feladat segítségével is futtathatja.

### <a name="score-in-azure-machine-learning-aml"></a>Az Azure-ban pontszám számítógép-képzési (AML)
Pontszám Azure Machine Learning segítségével, használja az úgynevezett nyílt forráskódú Azure Machine Learning R csomag [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) közzététele a modell Azure webszolgáltatásként. Kényelmi célokat szolgál a csomag nincs előre telepítve van a élcsomópont. Ezt követően az Azure Machine Learning létesítményekben hozhat létre a webszolgáltatás felhasználói felület, és majd a webszolgáltatás hívására, pontozó igény szerint.

Válassza ezt a beállítást, ha bármely ScaleR modellobjektumokat kell konvertálnia egyenértékű nyílt forráskódú modellobjektumokat a webszolgáltatáshoz való használatra. Használjon ScaleR kényszerítési funkciók, például `as.randomForest()` ensemble alapú modellek esetében az átalakításhoz.

### <a name="score-on-premises"></a>Helyszíni pontszám
A modell létrehozása után helyszíni pontozásához, szerializálni a modellnek az R, töltse le, deszerializálni, mert azt és kövesse az új adatok pontozása. Akkor is a korábban a leírt módszer segítségével új adatok pontozása [hdinsight pontozási](#scoring-in-hdinsight) vagy használatával [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>A fürt karbantartása

### <a name="install-and-maintain-r-packages"></a>Telepítheti és karbantarthatja R csomagok

Az R csomagokat használhat a legtöbb élcsomópont óta az R parancsfájlok nincs legtöbb lépések szükségesek. Élcsomópont további R csomagok telepítéséhez használhatja a szokásos `install.packages()` R. metódus

Ha rutinok a ScaleR könyvtárból a fürtön csak használ, nem általában telepítendő további R csomagokat az adatok csomópontján. Azonban szükség lehet további csomagok használatát támogató **rxExec** vagy **RxDataStep** végrehajtási adatok csomópontjára.

Ilyen esetben a további csomagok telepíthetők a parancsfájlművelet a fürt létrehozása után. További információkért lásd: [R-kiszolgáló kezelése a HDInsight-fürt](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Hadoop-MapReduce memória beállításainak módosítása

A fürt MapReduce feladatot futó R-kiszolgálóhoz elérhető memória mennyiségének módosítása kell módosítani. A fürt módosításához használja az Apache Ambari felhasználói felület, amely a fürt számára az Azure portál panel keresztül érhető el. A fürt felhasználói felülete Ambari elérésével kapcsolatos útmutatásért lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md).

Akkor is módosíthatja a Hadoop kapcsolók használatával hívásában R Server rendelkezésre álló memória mennyisége **RxHadoopMR** az alábbiak szerint:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Fürt méretezése

Egy meglévő R Server-fürt a HDInsight méretezhetők felfelé vagy lefelé a portálon keresztül. Vertikális felskálázásával, a további kapacitást, amelyek hasznosak lehetnek a nagyobb feldolgozási feladatok áttekintésével felmérheti, vagy méretezheti vissza egy fürt tétlen állapotában. A fürt méretezése kapcsolatos útmutatásért lásd: [kezelése HDInsight-fürtök](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>A rendszer karbantartása

Operációsrendszer-javítások és egyéb frissítés alkalmazásához karbantartást végeznek a HDInsight-fürtök a mögöttes Linux virtuális gépek munkaidőn kívüli során. Általában karbantartási történik, 3:30-kor (a virtuális gép számára a helyi ideje alapján), minden hétfőjén és csütörtökén. Úgy, hogy a fürt egyszerre több mint egy negyedéve nincs hatással lesznek a frissítések megtörténik.  

Mivel az átjárócsomópontokkal redundáns, és nem minden adatcsomópontokat érintettek, bármely ez idő alatt futó feladatok lelassíthatják. Azonban ezek továbbra is fusson befejezését. Bármely egyéni szoftvert vagy a helyi adatok, amelyekhez őrzi meg karbantartási eseményekből keresztül, kivéve, ha végzetes hiba lép fel, amely egy fürt újjáépítése szükséges.

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>IDE beállítások R Server on HDInsight-fürt

HDInsight-fürt Linux élcsomópont az R-alapú elemzése követően időzónáját. HDInsight újabb verzióiban a peremhálózati csomóponton Rstudióból kiszolgáló alapértelmezett telepítést biztosítanak, egy webböngésző-alapú IDE. Egy IDE a fejlesztési Rstudióból kiszolgáló használni és az R parancsfájlok végrehajtását lehet jelentősen hatékonyabb, mint az R-konzol használata.

Ezenkívül egy asztali IDE telepítése és hozzáférhetne a fürt egy távoli MapReduce vagy a Spark számítási környezet használata. A Microsoft a választható lehetőségek [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) Rstudióból és Walware tartozó Eclipse-alapú [StatET](http://www.walware.de/goto/statet).

Végül, az R-konzolra élcsomópont begépelésével érhetik el **R** SSH vagy PuTY segítségével történő kapcsolódás után Linux parancs parancssorba. Ha a konzol felhasználói felületén, célszerű egy szövegszerkesztőben R-parancsfájl fejlesztési futtassa egy másik ablakban, és Kivágás, és illessze be a parancsfájl szakaszok a R konzolba, igény szerint.

## <a name="pricing"></a>Díjszabás

Az R Server a HDInsight-fürtök társított árak hasonlóan a szabványos HDInsight-fürtök árak vannak felépítve. Az alapul szolgáló virtuális gépek méretezési a neve, adatokat és peremhálózati csomópontok, azonban kiegészül a core órás uplift alapulnak. HDInsight árazással kapcsolatos további információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>További lépések

R Server a HDInsight-fürtökön használatával kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [Ismerkedés az R Server fürttel hdinsighton](r-server-get-started.md)
* [Számítási R Server-fürt a HDInsight adatkörnyezet beállításai](r-server-compute-contexts.md)
* [A HDInsight fürt az R Serverhez Azure tárolási lehetőségek](r-server-storage.md)

---
title: Bevezetés az ML Azure hdinsight |} Microsoft Docs
description: Megtudhatja, hogyan ML szolgáltatások használata a HDInsight a big data elemző alkalmazások létrehozásához.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 9633a7df1cb72f3e9e5ee79be0c332565e7e8f2a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054102"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>Az ML-szolgáltatások és nyílt forráskódú R képességek a HDInsight bemutatása

> [!NOTE]
> 2017. szeptember, a Microsoft R Server jelent meg új neve alatt **Microsoft Machine Learning Server** vagy ML-kiszolgáló. Ennek következtében a HDInsight R Server-fürt neve **Machine Learning szolgáltatás** vagy **ML szolgáltatások** a HDInsight fürt. Az R kiszolgálónév-változás további információkért lásd: [Microsoft R Server lesz Microsoft Machine Learning kiszolgáló](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server).

Microsoft Machine Learning Server a HDInsight-fürtök létrehozása az Azure-ban esetén érhető el, mint a központi telepítési lehetőség. A fürt, amely ezt a lehetőséget biztosít neve **ML szolgáltatások**. Ezt a képességet biztosít a adatelemzők, statisztikusok és R-t használó programozók méretezhető, igény szerinti hozzáféréssel rendelkező elosztott elemzés hdinsight platformon történő módszerek.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

A HDInsight ML-szolgáltatások töltődnek be az Azure Blob vagy a Data Lake szinte bármilyen méretű adatkészletek a legújabb funkciókat az R-alapú használatával biztosít. ML szolgáltatások fürt nyílt forráskódú R épül, mivel az R-alapú alkalmazások létrehozása kihasználhatják a 8000 + nyílt forráskódú R csomagok bármelyikét. A feladatok az ScaleR, a Microsoft big data elemzés csomag is rendelkezésre állnak.

A fürt élcsomópont kényelmes csatlakozzon a fürthöz, és az R parancsfájlok futtatásához. Egy élcsomópontot az informatikai részleg parallelized elosztott feladatai ScaleR a peremhálózati kiszolgáló a magokon futtassák. Is futtathatja őket a fürt csomópontjai között ScaleR a Hadoop térkép csökkentése használatával, vagy Spark számítási környezeteket.

A modellek vagy előrejelzéseket elemzés a helyi használatra tölthető le. Akkor is is lehet operationalized máshol Azure, különösen keresztül [Azure Machine Learning Studio](http://studio.azureml.net) [webszolgáltatás](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Ismerkedés az ML-szolgáltatások hdinsight

Az Azure HDInsight az ML-szolgáltatások fürt létrehozásához válassza a **ML szolgáltatások** fürt típusa, az Azure portál használatával HDInsight-fürtök létrehozásakor. A gépi tanulás szolgáltatások fürt típusa tartalmaz ML-kiszolgálót, az adatok a fürt csomópontjai és egy élcsomópontot, az ML-alapú analytics követően zónának szolgál. Lásd: [első lépések az ML-szolgáltatások hdinsight](r-server-get-started.md) a fürt létrehozásához útmutatást.

## <a name="why-choose-ml-services-in-hdinsight"></a>Miért válassza ki az ML-szolgáltatások a Hdinsightban?

A HDInsight ML-szolgáltatások a következő előnyöket nyújtja:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>A Microsoft és nyílt forráskódú AI innováció

  ML-szolgáltatások részét képező magas szinten méretezhető, elosztott algoritmusok készleteiből állnak, mint [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), és [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , amely használható az adatok mérete nagyobb, mint a fizikai memória, és számos különböző platformokról elosztott módon futtassa mérete. Ismerje meg, további információk a Microsoft a gyűjteményhez tartozó egyéni [R csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) és [Python-csomagokat](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a termékhez mellékelt.
  
  Gépi tanulás szolgáltatások kulcsösszetevő ezek Microsoft újításait és érkező összes fölött egy egyetlen vállalati szintű platform (AI, R és Python eszközök gazdag) nyílt forráskódú közösségi hozzájárulásokat. Bármilyen R vagy Python nyílt forráskódú gépi tanulás csomagot és a Microsoft semmilyen szellemi tulajdont képező innováció használható.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Egyszerű, biztonságos és nagy méretű operationalization és felügyelet

  A vállalatok számára a hagyományos mintáknak és környezetekben függő fektetnek sok időt és erőfeszítést operationalization felé. Ez a felfújt költségeket eredményez, és késlelteti a fordítási idő modellek, hogy továbbra is érvényes az ismétlés és aktuális, szabályozási jóváhagyási, és kezelésére engedélyekkel a operationalization is beleértve.

  Gépi tanulás szolgáltatás nyújt enterprise osztályú [operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization), abban, hogy a gépi tanulási modell befejezése után néhány kattintással webszolgáltatások API-k létrehozásához szükséges. Ezek [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) server rácson a felhőben üzemeltetett és üzleti alkalmazásokkal integrálható. Központi telepítése egy vállalati, mind a kötegelt és a valós idejű pontozási igényekkel zökkenőmentesen méretezheti rugalmas rács lehetővé teszi. Útmutatásért lásd: [azok ML-szolgáltatások hdinsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>A HDInsight ML-szolgáltatások a legfontosabb jellemzők

A következő szolgáltatásokat tartalmazza az ML-szolgáltatások hdinsight.

| A szolgáltatás kategória | Leírás |
|------------------|-------------|
| R-engedélyezve | [R csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) R, az R, és a parancsfájl végrehajtása a futtatási infrastruktúra egy nyílt forráskódú terjesztési írt megoldások. |
| Python-engedélyezve | [Python-modulok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a Python és a parancsfájl végrehajtása a futtatási infrastruktúra egy nyílt forráskódú eloszláshoz, pythonban írt megoldások.
| [Előre betanított modellek](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | A vizuális elemzés és a szöveg véleményeket elemzésekhez készen áll az adatok pontozása megadnia. |
| [Központi telepítése és felhasználása](r-server-operationalize.md) | Azok a kiszolgáló, és állíthat rendszerbe megoldásokat webszolgáltatásként. |
| [Távoli végrehajtás](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Gépi tanulás szolgáltatások fürtön ügyfél a munkaállomáson a hálózaton működő távoli munkamenetek indításához. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>A HDInsight ML szolgáltatások adatok tárolási lehetőségek

A HDInsight-fürtök a HDFS fájlrendszerében alapértelmezett tároló vagy egy Azure Storage-fiókot, vagy egy Azure Data Lake Store társítható. Ez a társítás biztosítja, hogy az adatokat tölt fel a fürt, tárolás elemzési során állandó történik, és a fürtök törlése után is érhető el az adatokat. Számos különböző eszköz az adatok átvitele a tárolási lehetőség választ ki, beleértve a feltöltési portálalapú létesítmény a tárfiók kezelése és a [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogramot.

Lehetősége van további Blob való hozzáférés engedélyezése, és a Data lake tárolja a kiépítési folyamat függetlenül használja az elsődleges tárolási lehetőség fürt során. Lásd: [első lépések az ML-szolgáltatások hdinsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) további fiókokat ad hozzá hozzáférési olvashat. Lásd: [Azure tárolási lehetőségek az ML-szolgáltatások hdinsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) több storage-fiókok használatával kapcsolatos további olvashatja.

Is [Azure fájlok](../../storage/files/storage-how-to-use-files-linux.md) tárolási beállításként való használatra a peremhálózati csomóponton. Az Azure Files lehetővé teszi a fájlmegosztást, amelyet a Linux fájlrendszerben létrejött az Azure Storage csatlakoztatni. HDInsight-fürt ML szolgáltatások adatok tárolási lehetőségek kapcsolatos további információkért lásd: [Azure tárolási lehetőségek az ML-szolgáltatások hdinsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Hozzáférés ML szolgáltatások élcsomópont

Csatlakozhat a Microsoft ML Server, a peremhálózati csomóponton böngésző használatával. Telepíti a rendszer alapértelmezés szerint a fürt létrehozása során. További információkért lásd: [HDInsight ML szolgáltatásokkal beolvasása stared](r-server-get-started.md). Keresztül is csatlakozhat a fürtcsomópont peremhálózati a parancssorból az R-konzol eléréséhez a SSH/PuTTY használatával.

## <a name="develop-and-run-r-scripts"></a>Fejlesztéséhez és az R parancsfájlok futtatása

Az R parancsfájlok létrehozása és futtatása használhatja a 8000 + nyílt forráskódú R csomagok elérhető párhuzamos működésű és elosztott rutinok mellett a ScaleR könyvtárban. A élcsomópont ML szolgáltatásokkal futó parancsfájl általában az R parancsértelmező belül fut, ezen a csomóponton. A kivételek közé tartoznak a fenti lépések, amelyeket a beállított számítási környezetet ScaleR függvény Hadoop térkép csökkentése (RxHadoopMR) vagy a Spark (RxSpark). A függvény ebben az esetben egy elosztott módon fut, ezen adatok (feladat) csomópontokon a fürt társított hivatkozott adatokkal. A különböző számítási környezet beállításokkal kapcsolatos további információkért lásd: [adatkörnyezet beállításai az ML-szolgáltatások hdinsight számítási](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Modell üzembe helyezése

Az adatok modellezési befejeződése után az új adatokat a Azure vagy a helyszíni előrejelzéseket készítsen a modellt üzembe. Ez a folyamat pontozási néven ismert. Pontozó teheti HDInsight, Azure Machine Learning vagy a helyszínen.

### <a name="score-in-hdinsight"></a>Pontszám a Hdinsightban

Pontozásához a hdinsight eszközben, az R függvényt, amely meghívja a modell a tárfiók betöltött új adatfájl előrejelzéseket készítsen írni. Ezután menti az előrejelzés a tárfiók. A szokásos igény szerinti élcsomópont a fürt vagy egy ütemezett feladat segítségével is futtathatja.

### <a name="score-in-azure-machine-learning-aml"></a>Az Azure-ban pontszám számítógép-képzési (AML)

Pontszám Azure Machine Learning segítségével, használja az úgynevezett nyílt forráskódú Azure Machine Learning R csomag [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) közzététele a modell Azure webszolgáltatásként. Kényelmi célokat szolgál a csomag nincs előre telepítve van a élcsomópont. Ezt követően az Azure Machine Learning létesítményekben hozhat létre a webszolgáltatás felhasználói felület, és majd a webszolgáltatás hívására, pontozó igény szerint.

Válassza ezt a beállítást, ha bármely ScaleR modellobjektumokat kell konvertálnia egyenértékű nyílt forráskódú modellobjektumokat a webszolgáltatáshoz való használatra. Használjon ScaleR kényszerítési funkciók, például `as.randomForest()` ensemble alapú modellek esetében az átalakításhoz.

### <a name="score-on-premises"></a>Helyszíni pontszám

A modell létrehozása után helyszíni pontozásához, szerializálni a modellnek az R, töltse le, deszerializálni, mert azt és kövesse az új adatok pontozása. Akkor is a korábban a leírt módszer segítségével új adatok pontozása [hdinsight pontozási](#scoring-in-hdinsight) vagy használatával [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>A fürt karbantartása

### <a name="install-and-maintain-r-packages"></a>Telepítheti és karbantarthatja R csomagok

Az R csomagokat használhat a legtöbb élcsomópont óta az R parancsfájlok nincs legtöbb lépések szükségesek. Élcsomópont további R csomagok telepítéséhez használja a `install.packages()` R. metódus

Ha rutinok a ScaleR könyvtárból a fürtön csak használ, nem általában telepítendő további R csomagokat az adatok csomópontján. Azonban szükség lehet további csomagok használatát támogató **rxExec** vagy **RxDataStep** végrehajtási adatok csomópontjára.

Ilyen esetben a további csomagok telepíthetők a parancsfájlművelet a fürt létrehozása után. További információkért lásd: [ML-szolgáltatások kezelése a HDInsight-fürt](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Hadoop-MapReduce memória beállításainak módosítása

A fürt MapReduce feladatot futó ML szolgáltatásokban elérhető memória mennyiségének módosítása kell módosítani. A fürt módosításához használja az Apache Ambari felhasználói felület, amely a fürt számára az Azure portál panel keresztül érhető el. A fürt felhasználói felülete Ambari elérésével kapcsolatos útmutatásért lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md).

Akkor is módosíthatja a Hadoop kapcsolók használatával hívásában ML-szolgáltatások rendelkezésre álló memória mennyisége **RxHadoopMR** az alábbiak szerint:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Fürt méretezése

Egy meglévő ML-Services-fürttel hdinsighton méretezhetők felfelé vagy lefelé a portálon keresztül. Vertikális felskálázásával, a további kapacitást, amelyek hasznosak lehetnek a nagyobb feldolgozási feladatok áttekintésével felmérheti, vagy méretezheti vissza egy fürt tétlen állapotában. A fürt méretezése kapcsolatos útmutatásért lásd: [kezelése HDInsight-fürtök](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>A rendszer karbantartása

Operációsrendszer-javítások és egyéb frissítés alkalmazásához karbantartást végeznek a HDInsight-fürtök a mögöttes Linux virtuális gépek munkaidőn kívüli során. Általában karbantartási történik, 3:30-kor (a virtuális gép számára a helyi ideje alapján), minden hétfőjén és csütörtökén. Úgy, hogy a fürt egyszerre több mint egy negyedéve nincs hatással lesznek a frissítések megtörténik.

Mivel az átjárócsomópontokkal redundáns, és nem minden adatcsomópontokat érintettek, bármely ez idő alatt futó feladatok lelassíthatják. Azonban ezek továbbra is fusson befejezését. Bármely egyéni szoftvert vagy a helyi adatok, amelyekhez őrzi meg karbantartási eseményekből keresztül, kivéve, ha végzetes hiba lép fel, amely egy fürt újjáépítése szükséges.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>A HDInsight az ML-szolgáltatások IDE-beállítások

HDInsight-fürt Linux élcsomópont az R-alapú elemzése követően időzónáját. HDInsight újabb verzióiban a peremhálózati csomóponton Rstudióból kiszolgáló alapértelmezett telepítést biztosítanak, egy webböngésző-alapú IDE. Egy IDE a fejlesztési Rstudióból kiszolgáló használni és az R parancsfájlok végrehajtását lehet jelentősen hatékonyabb, mint az R-konzol használata.

Ezenkívül egy asztali IDE telepítése és hozzáférhetne a fürt egy távoli MapReduce vagy a Spark számítási környezet használata. A Microsoft a választható lehetőségek [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) Rstudióból és Walware tartozó Eclipse-alapú [StatET](http://www.walware.de/goto/statet).

Emellett akkor begépelésével érhetik el az R-konzolra élcsomópont **R** SSH vagy a PuTTY segítségével történő kapcsolódás után Linux parancs parancssorba. Ha a konzol felhasználói felületén, célszerű egy szövegszerkesztőben R-parancsfájl fejlesztési futtassa egy másik ablakban, és Kivágás, és illessze be a parancsfájl szakaszok a R konzolba, igény szerint.

## <a name="pricing"></a>Díjszabás

Az ML-szolgáltatások HDInsight-fürtök társított árak hasonlóan a más HDInsight-fürttípusok árakat van felépítve. Az alapul szolgáló virtuális gépek méretezési a neve, adatokat és peremhálózati csomópontok, azonban kiegészül a core órás uplift alapulnak. További információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>További lépések

A HDInsight-fürtökön ML-szolgáltatások használatával kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [Ismerkedés az ML Serices-fürttel hdinsighton](r-server-get-started.md)
* [Számítási környezet lehetőségek az ML-Services-fürttel hdinsighton](r-server-compute-contexts.md)
* [Tárolási lehetőségek az ML-Services-fürttel hdinsighton](r-server-storage.md)
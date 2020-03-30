---
title: Bevezetés az ML-szolgáltatásokba az Azure HDInsightban
description: Ismerje meg, hogyan hozhat létre alkalmazásokat big data-elemzéshez a HDInsight ML-szolgáltatások használatával.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77122366"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Mi az ML-szolgáltatások az Azure HDInsightban?

A Microsoft Machine Learning Server központi telepítési lehetőségként érhető el, amikor HDInsight-fürtöket hoz létre az Azure-ban. A lehetőséget nyújtó fürttípus neve **ML-szolgáltatások**. Ez a funkció lehetővé teszi, hogy az adatszakértők, statisztikusok és r-programozók igény szerinti hozzáférést biztosítanak a HDInsight méretezhető, elosztott elemzési módszereihez.

A HDInsight ML-szolgáltatásai a legújabb képességeket biztosítják az R-alapú elemzésekhez gyakorlatilag bármilyen méretű adatkészleteken, amelyek az Azure Blob vagy a Data Lake tárolóba töltődnek be. Mivel az ML Services fürt nyílt forráskódú R-re épül, a beépített R-alapú alkalmazások a 8000+ nyílt forráskódú R csomagok bármelyikét kihasználhatják. A ScaleR, a Microsoft big data-elemzési csomagrutinjai is elérhetők.

A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való csatlakozáshoz és az R-parancsfájlok futtatásához. Egy peremhálózati csomópont, lehetősége van a ScaleR párhuzamos elosztott függvényeinek futtatására a peremhálózati csomópont-kiszolgáló magjain. A ScaleR Hadoop-térképcsökkentés vagy az Apache Spark számítási környezeteinek használatával is futtathatja őket a fürt csomópontjain.

Az elemzésből származó modellek vagy előrejelzések letölthetők a helyszíni használatra. Az Azure [Machine Learning Studio (klasszikus)](https://studio.azureml.net) [webszolgáltatáson](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)keresztül máshol is működőképessé tehetők.

## <a name="get-started-with-ml-services-on-hdinsight"></a>Az ML-szolgáltatások első lépései a HDInsighton

Ml Services-fürt létrehozásához az Azure HDInsight, válassza ki az **ML Services** fürttípusát, amikor hdinsight-fürtöt hoz létre az Azure Portalon. Az ML Services fürttípus tartalmazza az ML-kiszolgálót a fürt adatcsomópontjain és egy peremhálózati csomóponton, amely az ML-szolgáltatások on-based elemzés célzónájaként szolgál. [Az Apache Hadoop-fürtök létrehozása az Azure Portalhasználatával](../hdinsight-hadoop-create-linux-clusters-portal.md) című témakörben ismerteti a fürt létrehozásának forgatókönyvét.

## <a name="why-choose-ml-services-in-hdinsight"></a>Miért válassza az ML-szolgáltatásokat a HDInsightban?

A HDInsight ML-szolgáltatásai a következő előnyöket biztosítják:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>A I innováció a Microsofttól és a nyílt forráskódú

  Az ML-szolgáltatások nagymértékben méretezhető, elosztott algoritmusokat tartalmaznak, például [a RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [a revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)és a [microsoftML,](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) amelyek a fizikai memória méreténél nagyobb adatméreteken működnek, és a platformok széles skáláján, elosztott módon futtathatók. További információ a Microsoft egyedi [R-csomagjainak](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) és [python-csomagjainak](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a termékhez mellékelt gyűjteményéről.
  
  Az ML Services egyetlen nagyvállalati szintű platformon hidalja át ezeket a Microsoft-innovációkat és a nyílt forráskódú közösségből (R, Python és AI-eszközök) származó hozzájárulásokat. Bármely R vagy Python nyílt forráskódú gépi tanulási csomag együtt működhet a Microsoft bármely saját fejlesztésével.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Egyszerű, biztonságos és nagy léptékű üzembe asszivalés és adminisztráció

  A hagyományos paradigmákra és környezetre támaszkodó vállalkozások sok időt és energiát fektetnek az operationalization befektetésébe. Ez növeli a költségeket és a késedelmeket, beleértve a modellek fordítási idejét, az érvényes és aktuális iterációkat, a hatósági jóváhagyást és az engedélyek kezelését a működés beadása révén.

  Az ML-szolgáltatások vállalati szintű [üzembe imitálást](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)kínálnak, mivel a gépi tanulási modell befejezése után mindössze néhány kattintásra van szükség a webszolgáltatások API-jainak létrehozásához. Ezek [a webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) a felhőben lévő kiszolgálórácson vannak tárolva, és integrálhatók az üzletági alkalmazásokkal. A rugalmas rácsra való üzembe helyezés lehetővé teszi, hogy zökkenőmentesen skálázható a vállalkozás igényeinek megfelelően, mind a kötegelt, mind a valós idejű pontozáshoz. További információt az [ML-szolgáltatások üzembe azon szolgáltatása a HDInsight-on (Operationalize ML Services on HDInsight) (A ml-szolgáltatások működőképessé tétele a HDInsight-on](r-server-operationalize.md)

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> A HDInsight ML Services fürttípusa csak a HDInsight 3.6-on támogatott. A HDInsight 3.6 a tervek szerint 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Az ML-szolgáltatások legfontosabb szolgáltatásai a HDInsightban

A HDInsight ML-szolgáltatásai az alábbi szolgáltatásokat tartalmazzák.

| Szolgáltatáskategória | Leírás |
|------------------|-------------|
| R-kompatibilis | [R-csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) az R-ben írt megoldásokhoz, az R nyílt forráskódú elosztásával, valamint a parancsfájlok végrehajtásához szükséges futásidejű infrastruktúrával. |
| Python-kompatibilis | [Python-modulok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) python-ban írt megoldásokhoz, a Python nyílt forráskódú disztribúciójával és a parancsfájlok végrehajtásához.
| [Előre betanított modellek](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Vizuális elemzéshez és szöveges hangulatelemzéshez készen áll a megadott adatok pontozására. |
| [Telepítés és felhasználás](r-server-operationalize.md) | Működőképessé a kiszolgálót, és megoldásokat telepíteni webszolgáltatásként. |
| [Távoli végrehajtás](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Indítsa el a távoli munkameneteket a hálózat ML Services fürtjén az ügyfél munkaállomásáról. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Az ML-szolgáltatások adattárolási lehetőségei a HDInsighton

A HDInsight-fürtök HDFS-fájlrendszerének alapértelmezett tárolása azure storage-fiókkal vagy Azure Data Lake Storage-szal társítható. Ez a társítás biztosítja, hogy az elemzés során a fürttárolóba feltöltött adatok állandóvá legyenek, és az adatok a fürt törlése után is elérhetők legyenek. Különböző eszközök állnak rendelkezésre a kiválasztott tárolási beállításra történő adatátvitel kezelésére, beleértve a tárfiók és az [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogram portálalapú feltöltési lehetőségét.

Lehetősége van további Blob és Data lake áruházak hoz való hozzáférés engedélyezésére a fürt létesítési folyamat során, függetlenül a ttól, hogy az elsődleges tárolási lehetőség használatban van.  Tekintse meg [az Azure Storage-beállítások ML Services HDInsight-cikkben](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) további többet a több tárfiókok használatával kapcsolatban.

Az Azure [Files](../../storage/files/storage-how-to-use-files-linux.md) storage-beállításként is használható a peremhálózati csomóponton. Az Azure Files lehetővé teszi, hogy az Azure Storage-ban létrehozott fájlmegosztást a Linux fájlrendszerhez csatlakoztassa. A HDInsight-fürt ml-szolgáltatásainak adattárolási lehetőségeiről az [Azure Storage options for ML Services on HDInsight](r-server-storage.md)című témakörben talál további információt.

## <a name="access-ml-services-edge-node"></a>Az ML-szolgáltatások peremhálózati csomópontjának elérése

A peremhálózati csomóponton lévő Microsoft ML-kiszolgálóhoz böngészővel csatlakozhat. Alapértelmezés szerint a fürt létrehozása során települ.  A fürt peremhálózati csomópontjához az R konzol eléréséhez az SSH/PuTTY használatával is csatlakozhat.

## <a name="develop-and-run-r-scripts"></a>R-parancsfájlok fejlesztése és futtatása

A létrehozott és futtatott R-parancsfájlok a ScaleR-tárban elérhető párhuzamos és elosztott rutinok mellett a 8000+ nyílt forráskódú R-csomagok bármelyikét használhatják. Általában egy parancsfájl, amely fut az ML-szolgáltatások a peremhálózati csomóponton fut az R értelmező az adott csomóponton. A kivételek azok a lépések, amelyek meg kell hívnia egy ScaleR függvény t, amely a Hadoop Map Reduce (RxHadoopMR) vagy spark (RxSpark) értékre van állítva. Ebben az esetben a függvény elosztott módon fut a fürt azon adat - (feladat) csomópontjai között, amelyek a hivatkozott adatokhoz vannak társítva. A különböző számítási környezet beállításairól a [Számítási környezet beállításai az ML-szolgáltatások számítási környezetbeállításai a HDInsight ban című témakörben talál](r-server-compute-contexts.md)további információt.

## <a name="operationalize-a-model"></a>Modell üzembe helyezése

Amikor az adatmodellezés befejeződött, üzembe hatja a modellt, hogy előrejelzéseket készítsen az új adatokhoz az Azure-ból vagy a helyszíni adatokról. Ezt a folyamatot pontozásnak nevezzük. A pontozás a HDInsightban, az Azure Machine Learningben vagy a helyszíni környezetben végezhető el.

### <a name="score-in-hdinsight"></a>Pontszám a HDInsightban

A HDInsight pontszámához írjon egy R-függvényt, amely meghívja a modellt, hogy előrejelzéseket készítsen egy új adatfájlhoz, amelyet betöltött a tárfiókba. Ezután mentse az előrejelzéseket vissza a tárfiókba. Ezt a rutint igény szerinti futtatásához a fürt peremhálózati csomópontján vagy egy ütemezett feladat használatával futtathatja.

### <a name="score-in-azure-machine-learning-aml"></a>Pontszám az Azure Machine Learningben (AML)

Az Azure Machine Learning használatával történő pontozáshoz használja az [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) néven ismert nyílt forráskódú Azure Machine Learning R csomagot a modell Azure-webszolgáltatásként való közzétételéhez. Az egyszerűség kedvéért ez a csomag előre telepítve van a peremhálózati csomóponton. Ezután használja az Azure Machine Learning létesítményeit egy felhasználói felület létrehozásához a webszolgáltatáshoz, majd hívja meg a webszolgáltatást, ha szükséges a pontozáshoz.

Ha ezt a beállítást választja, minden ScaleR modellobjektumot konvertálnia kell egyenértékű nyílt forráskódú modellobjektumokká a webszolgáltatással való használatra. Ehhez az átalakításhoz használja a `as.randomForest()` ScaleR kényszerítési függvényeket, például az együttesalapú modellekhez.

### <a name="score-on-premises"></a>Pontszám a helyszínen

A modell létrehozása után a helyszíni pontozáshoz szerializálhatja a modellt az R-ben, letöltheti, szerializálhatja, majd új adatok pontozásához használhatja. Új adatokat a Score a [HDInsightban](#score-in-hdinsight) korábban ismertetett módszerrel vagy [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)használatával szerezhet.

## <a name="maintain-the-cluster"></a>A fürt karbantartása

### <a name="install-and-maintain-r-packages"></a>R csomagok telepítése és karbantartása

A legtöbb használt R-csomag szükséges a peremhálózati csomóponton, mivel az R-parancsfájlok legtöbb lépése ott fut. További R-csomagok telepítéséhez a peremhálózati csomóponton használhatja az `install.packages()` R metódust.

Ha csak a ScaleR-tárból származó rutinokat használja a fürtön keresztül, általában nem kell további R-csomagokat telepítenie az adatcsomópontokra. Előfordulhat azonban, hogy további csomagokra van szükség az **rxExec** vagy az **RxDataStep** végrehajtásának az adatcsomópontokon történő használatához.

Ilyen esetekben a további csomagok a fürt létrehozása után parancsfájlművelettel telepíthetők. További információt a [Ml-szolgáltatások kezelése a HDInsight-fürtben című témakörben talál.](r-server-hdinsight-manage.md)

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Az Apache Hadoop MapReduce memóriabeállításainak módosítása

A fürt módosítható, hogy módosítsa a MapReduce feladat futtatásakor az ML-szolgáltatások számára elérhető memória mennyiségét. Fürt módosításához használja az Apache Ambari felhasználói felületét, amely elérhető az Azure Portal panelen keresztül a fürthöz. A fürt Ambari felhasználói felületének eléréséről a [HDInsight-fürtök kezelése az Ambari webfelhasználói felülethasználatával című](../hdinsight-hadoop-manage-ambari.md)témakörben talál.

Az ML-szolgáltatások számára rendelkezésre álló memória mennyisége is módosítható az **RxHadoopMR** hívásában lévő Hadoop kapcsolók használatával az alábbiak szerint:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>A fürt méretezése

A HDInsight meglévő ML-szolgáltatások fürtje a portálon keresztül felfelé vagy lefelé skálázható. A felskálázás, akkor szerezhet a további kapacitást, hogy szükség lehet a nagyobb feldolgozási feladatok, vagy a fürt méretezése, ha tétlen. A fürtök méretezéséről a [HDInsight-fürtök kezelése témakörben talál útmutatást.](../hdinsight-administer-use-portal-linux.md)

### <a name="maintain-the-system"></a>A rendszer karbantartása

Az operációs rendszer javításainak és egyéb frissítéseknek a karbantartása az alapul szolgáló Linux-virtuális gépeken történik egy HDInsight-fürtben munkaidőn kívül. A karbantartás általában minden hétfőn és csütörtökön 03:30-kor történik (a virtuális gép helyi ideje alapján). A frissítések végrehajtása oly módon történik, hogy azok egyszerre csak a fürt negyedét érintik.

Mivel a fő csomópontok redundánsak, és nem minden adatcsomópontot érint, az ebben az időszakban futó feladatok lelassulhatnak. Azonban továbbra is a befejezésig kell futniuk. Minden egyéni szoftver vagy helyi adatokat, amely rendelkezik megmarad nak ezek ben a karbantartási események, kivéve, ha egy katasztrofális hiba történik, amely megköveteli a fürt újraépítése.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-beállítások az ML-szolgáltatásokhoz a HDInsight-on

A HDInsight-fürt Linux peremhálózati csomópontja az R-alapú elemzés leszállási zónája. A HDInsight legújabb verziói az RStudio Server alapértelmezett telepítését biztosítják a peremhálózati csomóponton böngészőalapú IDE-ként. Az RStudio Server használata IDE-ként az R parancsfájlok fejlesztéséhez és végrehajtásához lényegesen hatékonyabb lehet, mint az R konzol használata.

Emellett telepítheti az asztali IDE-t, és használhatja a fürt eléréséhez egy távoli MapReduce vagy Spark számítási környezet használatával. A lehetőségek közé tartozik a Microsoft [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio, és a Walware Eclipse-alapú [StatET](http://www.walware.de/goto/statet).

Ezenkívül az R konzolt a peremhálózati csomóponton érheti el, ha az **SSH** vagy PuTTY rendszeren keresztüli csatlakozás után beírja az R parancsot a Linux parancssorába. A konzolfelület használatakor célszerű egy szövegszerkesztőt futtatni az R-parancsfájl fejlesztéséhez egy másik ablakban futtatni, és szükség szerint kivágni és beilleszteni a parancsfájl szakaszait az R konzolba.

## <a name="pricing"></a>Díjszabás

Az ML Services HDInsight-fürthöz társított árak a többi HDInsight-fürttípus áraihoz hasonlóan vannak felépítve. Ezek alapján az alapul szolgáló virtuális gépek a név, az adatok és a peremhálózati csomópontok, core-hour uplift hozzáadásával. További információt a [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/)című témakörben talál.

## <a name="next-steps"></a>További lépések

Az ML-szolgáltatások HDInsight-fürtökön való használatáról az alábbi témakörökben olvashat bővebben:

* [R-parancsfájl végrehajtása ml-szolgáltatások fürtén az Azure HDInsightban az RStudio Server használatával](machine-learning-services-quickstart-job-rstudio.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [Az ML Services fürt tárolási lehetőségei a HDInsighton](r-server-storage.md)

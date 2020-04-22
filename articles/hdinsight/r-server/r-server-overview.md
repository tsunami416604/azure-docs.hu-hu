---
title: Bevezetés az ML-szolgáltatásokba az Azure HDInsightban
description: Ismerje meg, hogyan hozhat létre alkalmazásokat big data-elemzéshez a HDInsight ML-szolgáltatások használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 76fcdb52df88be2c4033140f4bc71b28424d7f38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687794"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Mi az ML-szolgáltatások az Azure HDInsightban?

A Microsoft Machine Learning Server központi telepítési lehetőségként érhető el, amikor HDInsight-fürtöket hoz létre az Azure-ban. A lehetőséget nyújtó fürttípus neve **ML-szolgáltatások**. Ez a funkció igény szerinti hozzáférést biztosít a HDInsight adaptálható, elosztott elemzési módszereihez.

A HDInsight ML-szolgáltatásai a legújabb képességeket biztosítják a gyakorlatilag bármilyen méretű adatkészletek r-alapú elemzéséhez. Az adatkészletek betölthetők az Azure Blob vagy a Data Lake storage. Az R-alapú alkalmazások használhatják a 8000+ nyílt forráskódú R csomagokat. A ScaleR, a Microsoft big data-elemzési csomagrutinjai is elérhetők.

A peremhálózati csomópont kényelmes helyet biztosít a fürthöz való csatlakozáshoz és az R-parancsfájlok futtatásához. A peremhálózati csomópont lehetővé teszi a ScaleR párhuzamosan elosztott elosztott függvények futtatását a kiszolgáló magjain. A ScaleR Hadoop-térképcsökkentése használatával is futtathatja őket a fürt csomópontjain. Az Apache Spark számítási környezeteit is használhatja.

Az elemzésből származó modellek vagy előrejelzések letölthetők a helyszíni használatra. Az Azure-ban is lehetnek `operationalized` máshol. Különösen az [Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net)és [a webszolgáltatás](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)révén.

## <a name="get-started-with-ml-services-on-hdinsight"></a>Az ML-szolgáltatások első lépései a HDInsighton

Ml Services-fürt létrehozásához a HDInsightban válassza az **ML Services** fürttípusát. Az ML-szolgáltatások fürttípusa tartalmazza az ML-kiszolgálót az adatcsomópontokon és a peremhálózati csomópontot. A peremhálózati csomópont az ML Services-alapú elemzés ek leszállási zónájaként szolgál. [Az Apache Hadoop-fürtök létrehozása az Azure Portalhasználatával](../hdinsight-hadoop-create-linux-clusters-portal.md) című témakörben ismerteti a fürt létrehozásának forgatókönyvét.

## <a name="why-choose-ml-services-in-hdinsight"></a>Miért válassza az ML-szolgáltatásokat a HDInsightban?

A HDInsight ML-szolgáltatásai a következő előnyöket biztosítják:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>A I innováció a Microsofttól és a nyílt forráskódú

  Az ML-szolgáltatások nagymértékben adaptálható, elosztott algoritmusokat tartalmaznak, például [a RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [a revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)és a [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package). Ezek az algoritmusok a fizikai memória méreténél nagyobb adatméreteken is működhetnek. Ők is futnak a legkülönbözőbb platformokon egy elosztott módon. További információ a Microsoft egyedi [R-csomagjainak](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) és [python-csomagjainak](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a termékhez mellékelt gyűjteményéről.
  
  Az ML Services áthidalja a microsoftos újításokat és a nyílt forráskódú közösségből (R, Python és AI-eszköztárak) származó hozzájárulásokat. Mindezt egyetlen nagyvállalati szintű platform tetején. Bármely R vagy Python nyílt forráskódú gépi tanulási csomag együtt működhet a Microsoft bármely saját fejlesztésével.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Egyszerű, biztonságos és nagy léptékű üzembe asszivalés és adminisztráció

  A hagyományos paradigmákra és környezetre támaszkodó vállalkozások sok időt és energiát fektetnek az operationalization befektetésébe. Ez a művelet növeli a költségeket és a késedelmeket, beleértve a fordítási időt: modellek, iterációk, hogy tartsa őket érvényes és aktuális, a hatósági jóváhagyás és az engedélyek kezelése.

  Az ML-szolgáltatások vállalati szintű [üzembe asszidást kínálnak.](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) A gépi tanulási modell befejezése után mindössze néhány kattintásra van szükség a webszolgáltatások API-jainak létrehozásához. Ezek [a webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) a felhőben lévő kiszolgálórácson vannak tárolva, és integrálhatók az üzletági alkalmazásokkal. A rugalmas rácsra való üzembe helyezés lehetővé teszi, hogy zökkenőmentesen skálázható a vállalkozás igényeinek megfelelően, mind a kötegelt, mind a valós idejű pontozáshoz. További információt az [ML-szolgáltatások üzembe azon szolgáltatása a HDInsight-on (Operationalize ML Services on HDInsight) (A ml-szolgáltatások működőképessé tétele a HDInsight-on](r-server-operationalize.md)

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
| Python-kompatibilis | [Python-modulok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) python-ban írt megoldásokhoz, a Python nyílt forráskódú disztribúciójával és a parancsfájlok végrehajtásához futó infrastruktúra használatával.
| [Előre betanított modellek](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Vizuális elemzéshez és szöveges hangulatelemzéshez készen áll a megadott adatok pontozására. |
| [Telepítés és felhasználás](r-server-operationalize.md) | `Operationalize`a kiszolgálót, és webszolgáltatásként telepítheti a megoldásokat. |
| [Távoli végrehajtás](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Indítsa el a távoli munkameneteket a hálózat ML Services fürtjén az ügyfél munkaállomásáról. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Az ML-szolgáltatások adattárolási lehetőségei a HDInsighton

A HDFS fájlrendszer alapértelmezett tárhelye lehet Egy Azure Storage-fiók vagy az Azure Data Lake Storage. Az elemzés során a fürttárolóba feltöltött adatok állandóvá lesznek tették. Az adatok a fürt törlése után is elérhetők. Különböző eszközök képesek kezelni az adatátvitelt a tárolóba. Az eszközök közé tartozik a tárfiók portálalapú feltöltési szolgáltatása és az AzCopy segédprogram.

A fürt létrehozása során további Blob és Data lake áruházakhoz is engedélyezhet hozzáférést. Nincs korlátozva az elsődleges tárolási lehetőség használatban.  Tekintse meg [az Azure Storage-beállítások ML Services HDInsight-cikkben](./r-server-storage.md) további többet a több tárfiókok használatával kapcsolatban.

Az Azure Files storage-beállításként is használható a peremhálózati csomóponton. Az Azure Files lehetővé teszi az Azure Storage-ban létrehozott fájlmegosztásokat a Linux fájlrendszer számára. További információ: [Azure Storage options for ML Services on HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Az ML-szolgáltatások peremhálózati csomópontjának elérése

A peremhálózati csomóponton lévő Microsoft ML-kiszolgálóhoz böngészővel vagy SSH/PuTTY használatával csatlakozhat. Az R konzol alapértelmezés szerint telepítve van a fürt létrehozása során.  

## <a name="develop-and-run-r-scripts"></a>R-parancsfájlok fejlesztése és futtatása

Az R szkriptek a 8000+ nyílt forráskódú R csomagok bármelyikét használhatják. A ScaleR-könyvtár párhuzamos és elosztott rutinjait is használhatja. A peremhálózaton futó parancsfájlok az r-értelmezőn belül futnak az adott csomóponton. Kivéve azokat a lépéseket, amelyek a ScaleR-függvényeket map reduce (RxHadoopMR) vagy Spark (RxSpark) számítási környezetben szólítják meg. A függvények az adatokhoz társított adatcsomópontok között elosztott módon futnak. A környezetbeállításokról a [Compute Context options for ML Services on HDInsight című témakörben talál](r-server-compute-contexts.md)további információt.

## <a name="operationalize-a-model"></a>`Operationalize`egy modell

Amikor az adatmodellezés befejeződött, `operationalize` a modell előrejelzéseket készít az azure-ból vagy a helyszíni új adatokhoz. Ezt a folyamatot pontozásnak nevezzük. A pontozás a HDInsightban, az Azure Machine Learningben vagy a helyszíni környezetben végezhető el.

### <a name="score-in-hdinsight"></a>Pontszám a HDInsightban

A HDInsight pontszámához írjon egy R-függvényt. A függvény meghívja a modellt, hogy előrejelzéseket készítsen egy új adatfájlhoz, amelyet betöltött a tárfiókba. Ezután mentse az előrejelzéseket vissza a tárfiókba. Ezt a rutint igény szerinti futtatásához a fürt peremhálózati csomópontján vagy egy ütemezett feladat használatával futtathatja.

### <a name="score-in-azure-machine-learning-aml"></a>Pontszám az Azure Machine Learningben (AML)

Az Azure Machine Learning használatával történő pontozáshoz használja az [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) néven ismert nyílt forráskódú Azure Machine Learning R csomagot a modell Azure-webszolgáltatásként való közzétételéhez. Az egyszerűség kedvéért ez a csomag előre telepítve van a peremhálózati csomóponton. Ezután használja az Azure Machine Learning létesítményeit egy felhasználói felület létrehozásához a webszolgáltatáshoz, majd hívja meg a webszolgáltatást, ha szükséges a pontozáshoz. Ezután konvertálja a ScaleR modellobjektumokat egyenértékű nyílt forráskódú modellobjektumokká a webszolgáltatással való használatra. Ehhez az átalakításhoz használja a `as.randomForest()` ScaleR kényszerítési függvényeket, például az együttesalapú modellekhez.

### <a name="score-on-premises"></a>Pontszám a helyszínen

A modell létrehozása után a helyszíni pontozáshoz: szerializálja a modellt az R-ben, töltse le, szerializálja, majd használja az új adatok pontozásához. Új adatokat a Score a HDInsightban korábban ismertetett módszerrel vagy [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)használatával szerezhet.

## <a name="maintain-the-cluster"></a>A fürt karbantartása

### <a name="install-and-maintain-r-packages"></a>R csomagok telepítése és karbantartása

A legtöbb használt R-csomag szükséges a peremhálózati csomóponton, mivel az R-parancsfájlok legtöbb lépése ott fut. További R-csomagok telepítéséhez a peremhálózati csomóponton használhatja az `install.packages()` R metódust.

Ha csak scaler könyvtári rutinokat használ, általában nincs szüksége további R-csomagokra. Előfordulhat, hogy további csomagokra van szükség az **rxExec** vagy **az RxDataStep** végrehajtásához az adatcsomópontokon.

A további csomagok a fürt létrehozása után parancsfájlművelettel telepíthetők. További információt a [Ml-szolgáltatások kezelése a HDInsight-fürtben című témakörben talál.](r-server-hdinsight-manage.md)

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Az Apache Hadoop MapReduce memóriabeállításainak módosítása

Az ML-szolgáltatások számára rendelkezésre álló memória módosítható, ha MapReduce feladatot futtat. Fürt módosításához használja az Apache Ambari felhasználói felületét a fürthöz. Az Ambari felhasználói felületével kapcsolatos utasításokat a [HDInsight-fürtök kezelése az Ambari webes felhasználói felülethasználatával című témakörben](../hdinsight-hadoop-manage-ambari.md)találja.

Az ML-szolgáltatások rendelkezésre álló memóriája az **RxHadoopMR**hívásában a Hadoop kapcsolókkal módosítható:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>A fürt méretezése

A HDInsight meglévő ML-szolgáltatások fürtje a portálon keresztül felfelé vagy lefelé skálázható. A felskálázással további kapacitást kap a nagyobb feldolgozási feladatokhoz. A fürt ötlött, és visszaskálázhatja a fürtöt. A fürtök méretezéséről a [HDInsight-fürtök kezelése témakörben talál útmutatást.](../hdinsight-administer-use-portal-linux.md)

### <a name="maintain-the-system"></a>A rendszer karbantartása

Az operációs rendszer karbantartása az alapul szolgáló Linux-virtuális gépeken történik egy HDInsight-fürtben munkaidőn kívül. A karbantartás általában minden hétfőn és csütörtökön 03:30-kor (helyi idő szerint) történik. A frissítések egyszerre csak a fürt negyedét érintik.

A karbantartási feladatok futtatása lelassulhat a karbantartás során. Azonban továbbra is a befejezésig kell futniuk. A karbantartási események során megőrződött egyéni szoftverek vagy helyi adatok megőrződnek, kivéve, ha a fürt újraépítését igénylő katasztrofális hiba következik be.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-beállítások az ML-szolgáltatásokhoz a HDInsight-on

A HDInsight-fürt Linux peremhálózati csomópontja az R-alapú elemzés leszállási zónája. A HDInsight legújabb verziói az RStudio Server böngészőalapú IDE-jét biztosítják a peremhálózati csomóponton. Az RStudio Server hatékonyabb, mint az R konzol a fejlesztéshez és a végrehajtáshoz.

Az asztali IDE távoli MapReduce vagy Spark számítási környezeten keresztül érheti el a fürtöt. A lehetőségek a következők: a Microsoft [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio és Walware's Eclipse-alapú StatET.

Az R konzol elérése a peremcsomópont szélén az **R** parancssorba beírásával. A konzolfelület használatakor célszerű R-parancsfájlt fejleszteni egy szövegszerkesztőben. Ezután szükség szerint vágja ki és illessze be a parancsfájl szakaszait az R konzolba.

## <a name="pricing"></a>Díjszabás

Az ML Services HDInsight-fürthöz társított árak a többi HDInsight-fürttípushoz hasonlóan strukturáltak. Az alapul szolgáló virtuális gépek méretezésén alapulnak a név, az adatok és a peremhálózati csomópontok között. A magórás emelések is. További információt a [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/)című témakörben talál.

## <a name="next-steps"></a>További lépések

Az ML-szolgáltatások HDInsight-fürtökön való használatáról az alábbi cikkekben olvashat bővebben:

* [R-parancsfájl végrehajtása ml-szolgáltatások fürtén az Azure HDInsightban az RStudio Server használatával](machine-learning-services-quickstart-job-rstudio.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [Az ML Services fürt tárolási lehetőségei a HDInsighton](r-server-storage.md)

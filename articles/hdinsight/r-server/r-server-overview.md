---
title: Az Azure HDInsight a Machine Learning Services bemutatása
description: 'Útmutató: Machine Learning-szolgáltatások használata a HDInsight a big data elemző alkalmazások létrehozásához.'
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a07f052cf89da039bb9fe091f1cd997c19d2fcc7
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584530"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>Machine Learning-szolgáltatások és a nyílt forráskódú R funkciókat biztosít a HDInsight bemutatása

> [!NOTE]  
> 2017 szeptemberi, a Microsoft R Server jelent meg új neve alatt megjelenő **Microsoft Machine Learning-kiszolgáló** vagy a Machine Learning-kiszolgáló. Ennek következtében, a HDInsight R Server-fürt új neve **Machine Learning-szolgáltatások** vagy **ML szolgáltatásokkal** a HDInsight-fürt. Az R Server névváltozásai további információkért lásd: [Microsoft R Server már Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server).

Microsoft Machine Learning-kiszolgáló telepítési beállításként érhető el, az Azure HDInsight-fürtök létrehozásakor. A fürt típusát, amely ezt a lehetőséget biztosít nevezzük **ML szolgáltatásokkal**. Ez a képesség nyújt, az adatszakértők, statisztikusok és R-programozók skálázható, igény szerinti hozzáféréssel rendelkező elosztott elemzési a HDInsight módszereket.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Machine Learning szolgáltatások a HDInsight betölti az Azure Blob vagy a Data Lake storage gyakorlatilag bármilyen méretű adatkészletek a legújabb funkciókat az R-alapú elemzési nyújt. Machine Learning-szolgáltatások fürt nyílt forráskódú R épül, mivel az R-alapú alkalmazásokat fejleszt kihasználhatják a 8000-es + nyílt forráskódú R csomagok. A feladatok az ScaleR, a Microsoft big data analytics csomag is rendelkezésre állnak.

A fürt élcsomópontjához kényelmes megoldás az R-szkriptek futtatása és a fürthöz való csatlakozáshoz. Az élcsomópont lehetősége van a futó párhuzamos működésű elosztott funkcióit ScaleR a Processzormagok száma, a peremhálózati kiszolgáló között. Is futtathatja őket a fürt csomópontjai között ScaleR a Hadoop Mapreduce vagy az Apache Spark számítási környezetek használatával.

A modellek vagy elemzési eredő előrejelzéseket letölthető a helyszíni használatra. Ezek is is kell üzembe helyezte azt máshol az Azure-ban, különös tekintettel keresztül [Azure Machine Learning Studio](https://studio.azureml.net) [webszolgáltatás](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Machine Learning Services a HDInsight használatának első lépései

Az Azure HDInsight egy Machine Learning-szolgáltatások-fürt létrehozásához válassza a **Machine Learning-szolgáltatások** fürt típusát az Azure portal használatával egy HDInsight-fürt létrehozásakor. A Machine Learning-szolgáltatások fürttípus ML Server is tartalmaz, az adatcsomópontok a fürt és a egy élcsomópontot, szolgál a Machine Learning-alapú elemzési alkotóelemeit zónának. Lásd: [Machine Learning Services a HDInsight használatának első lépései](r-server-get-started.md) bemutató hogyan hozhat létre a fürtöt.

## <a name="why-choose-ml-services-in-hdinsight"></a>Miért válassza ki a Machine Learning-szolgáltatások, a HDInsight?

Machine Learning szolgáltatások a HDInsight az alábbi előnyöket nyújtja:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>A Microsoft és a nyílt forráskódú AI innováció

  Machine Learning-szolgáltatások tartalmaz nagy mértékben skálázható, elosztott algoritmusokkal például [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), és [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) -nál nagyobb méretű adatokkal is dolgozhat, amely a fizikai memória, és futtassa a számos különböző platformok elosztott módon mérete. Ismerje meg, további információt a Microsoft gyűjteményét címzettjének egyéni [R-csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) és [Python-csomagok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a termékhez mellékelt.
  
  Machine Learning-szolgáltatások áthidalja ezeket a Microsoft innovációkat, és a hozzájárulások származó összes felett egy egyetlen vállalati szintű platform a nyílt forráskódú Közösség (R, Python és AI-eszközkészlettel). Bármely R vagy Python nyílt forráskódú gépi tanulási csomagot minden olyan szellemi tulajdont képező innováció a Microsoft párhuzamosan lesz képes együttműködni.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Egyszerű, biztonságos és nagy méretű operacionalizálás és felügyelet

  A hagyományos paradigmákat és környezetek függő vállalatok fektet mennyi idő és munka, operacionalizálás felé. Ez a felfújt költségeket eredményez, és később, beleértve a fordítási idő modellek, az ismétlések, hogy érvényes és aktuális, szabályozási jóváhagyási és a porttovábbításon keresztül engedélyek kezelése.

  Machine Learning-szolgáltatásokat kínál a nagyvállalati szintű [operacionalizálás](https://docs.microsoft.com/machine-learning-server/what-is-operationalization), abban, hogy egy machine learning-modellek befejezése után csupán néhány kattintással web services API-k létrehozásához vesz igénybe. Ezek [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) kiszolgáló rácshoz a felhőben üzemeltetett és integrálható az üzletági alkalmazások. Helyezze üzembe a kötegelt és valós idejű pontozási egyaránt üzleti igényeinek megfelelő, zökkenőmentes méretezése, egy rugalmas grid lehetővé teszi lehetővé teszi. Útmutatásért lásd: [Machine Learning-szolgáltatások üzembe helyezése a HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>A Machine Learning szolgáltatások a HDInsight fő funkciók

A Machine Learning szolgáltatások a HDInsight a következő szolgáltatásokat tartalmazza.

| A szolgáltatás kategória | Leírás |
|------------------|-------------|
| R-kompatibilis | [R-csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) egy nyílt forráskódú R-t és a parancsprogramok futtatásához futásidejű infrastruktúra eloszlása az R nyelven írt megoldásokhoz. |
| Python-kompatibilis | [Python-modulok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) pythonban írt, egy nyílt forráskódú elosztásáról, Python és a parancsprogramok futtatásához futásidejű infrastruktúra-megoldások számára.
| [Előre betanított modellek](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Vizuális elemzéshez és szöveg hangulatelemzése készen áll az adatok pontozása, adja meg. |
| [Üzembe helyezése és felhasználása](r-server-operationalize.md) | A kiszolgáló üzembe helyezése és webszolgáltatásként helyezhet üzembe megoldásokat. |
| [Távoli végrehajtás](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Indítsa el a távoli asztali munkamenetek a Machine Learning-szolgáltatások fürt ügyfél a munkaállomáson a hálózaton. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>A HDInsight Machine Learning-szolgáltatások adatok tárolási lehetőségek

Alapértelmezett tároló a HDInsight-fürtök a HDFS-fájlrendszer vagy egy Azure Storage-fiókot, vagy egy Azure Data Lake Storage társítható. Ez a társítás biztosítja, hogy feltöltött adatokat a fürthöz a tároló elemzése során állandó történik, és az adatok érhető el a fürt törlése után is. Az adatok átvitelét a beállítást választ ki, beleértve a portálalapú feltöltési funkció a storage-fiók kezeléséhez különféle eszközök és a [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogramot.

Lehetősége van további Blob való hozzáférés engedélyezése, és a Data lake tárolja az folyamat függetlenül használja az elsődleges tárolási lehetőség fürtkiépítési során. Lásd: [Machine Learning Services a HDInsight használatának első lépései](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) való hozzáadásáról a hozzáférés további fiókokra. Lásd: [Azure tárolási lehetőségei Machine Learning-szolgáltatások HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) cikk további több tárfiók használatával kapcsolatban.

Is [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) tárolási beállításként való használatra az élcsomóponti operacionalizáláshoz. Az Azure Files lehetővé teszi az Azure Storage-ban a Linux fájlrendszerbe létrehozott fájlmegosztást. HDInsight-fürtön a Machine Learning Services ezen adatok tárolási lehetőségekről további információkért lásd: [Azure tárolási lehetőségei Machine Learning-szolgáltatások HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Hozzáférés a Machine Learning-szolgáltatások élcsomóponthoz

Csatlakozhat a Microsoft ML Server, az élcsomóponton, egy böngészővel. Telepíti a rendszer alapértelmezés szerint a fürt létrehozásakor. További információkért lásd: [lekérése és a gépi Tanulási szolgáltatások HDInsight stared](r-server-get-started.md). Akkor is csatlakozhat a fürt határcsomópontjához a parancssorból az R-konzol eléréséhez az SSH és PuTTY használatával.

## <a name="develop-and-run-r-scripts"></a>Fejlesztés és R-szkriptek futtatása

Az R-szkriptek létrehozása és futtatása bármelyikét használhatja a 8000-es + nyílt forráskódú R-csomagok mellett a rendelkezésre álló párhuzamos és elosztott rutinokat a ScaleR-könyvtárban. A Machine Learning-szolgáltatásokkal, az élcsomóponton futtatott parancsfájl általában az R-értelmező belül futtatja, ezen a csomóponton. A kivételek közé tartoznak, amely egy beállított számítási környezet ScaleR függvény meghívásához szükséges lépések, Hadoop Mapreduce (RxHadoopMR) vagy a Sparkkal (RxSpark). Ebben az esetben a függvény fut egyszerre egy elosztott módon ezen adatok (feladat) csomópontok, a fürt, amely a hivatkozott adatokkal vannak társítva. A különböző számítási környezeti beállítások kapcsolatos további információkért lásd: [számítási környezeti beállítások a Machine Learning-szolgáltatásokhoz a HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Modell üzembe helyezése

Ha elkészült az adatmodellezés, működésbe hozhat a modellt, hogy az új adatok vagy az Azure-ban vagy a helyi adatokat. Ez a folyamat pontozási néven ismert. A pontozás elvégezhető a HDInsight, Azure Machine Learning vagy a helyszínen.

### <a name="score-in-hdinsight"></a>A HDInsight pontszám

A pontszám a HDInsight, egy R-függvényt, amely meghívja ezt a modellt, hogy egy új adatfájlt a tárfiók betöltött adatokat írni. Mentse az előrejelzés térjen vissza a tárfiók. Rendszeres igény szerinti, az élcsomóponton, a fürt vagy egy ütemezett feladat használatával futtathatja.

### <a name="score-in-azure-machine-learning-aml"></a>Az Azure-ban pontszám a Machine Learning (AML)

Pontszámot rendelni az Azure Machine Learning segítségével, használja a nyílt forráskódú Azure Machine Learning R csomag néven [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) közzététele a modell egy Azure-webszolgáltatásként. Az egyszerűség kedvéért ez a csomag az élcsomóponti operacionalizáláshoz előzetesen már telepítve. Ezután egy felhasználói felületet a webszolgáltatás létrehozásához használja az a funkciók az Azure Machine Learning, és majd hívja meg a webszolgáltatás pontozó igény szerint.

Ha ezt a lehetőséget választja, minden ScaleR adatmodell-objektumokat kell konvertálnia egyenértékű nyílt forráskódú modellobjektumokat a webszolgáltatáshoz való használatra. Használjon ScaleR-függvényei kényszert, például `as.randomForest()` ensemble-alapú modell az átalakításhoz.

### <a name="score-on-premises"></a>Helyszíni pontszám

A modell létrehozása után helyszíni pontozásához, szerializálni a modell az R, töltse le, deszerializálható, és majd használni az új adatok pontozásához. Ön is a korábban ismertetett megközelítés az új adatok pontozása [HDInsight pontszámot](#score-in-hdinsight) vagy [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>A fürt kezelése

### <a name="install-and-maintain-r-packages"></a>Telepítse és R-csomagok kezelése

Az R-csomagok használata a legtöbb óta a lépések többségét, az R-szkriptek futtatása van az élcsomópontra van szükség. További R csomagokat telepíteni az élcsomóponton, használhatja a `install.packages()` metódus az R.

Ha rutinokat a ScaleR-erőforrástárból a fürtben csak használja, akkor sem általában kell további R csomagokat telepítenie az adatcsomópontok. Azonban szükség lehet további csomagok használatát támogató **rxExec** vagy **RxDataStep** végrehajtás a data-csomópontokon.

Ezekben az esetekben a további csomagok telepíthetők szkriptműveletet a fürt létrehozása után. További információkért lásd: [Machine Learning Services kezelése a HDInsight-fürt](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Az Apache Hadoop MapReduce memória beállításainak módosítása

A fürt módosíthatók, ha az egy MapReduce-feladatot a Machine Learning-szolgáltatások rendelkezésre álló memória mennyiségének módosítása. A fürt módosításához használja az Apache Ambari felhasználói felületén, amely a fürt számára az Azure portal paneljén érhető el. A fürt Ambari felhasználói felületén elérésével kapcsolatos útmutatásért lásd: [kezelése a HDInsight-fürtök az Ambari webes kezelőfelületen](../hdinsight-hadoop-manage-ambari.md).

Az is lehet módosítani a hívást a Hadoop-kapcsolók használatával Machine Learning-szolgáltatások rendelkezésre álló memória mennyisége **RxHadoopMR** módon:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Fürt méretezése

Machine Learning Services meglévő fürt a HDInsight méretezhetők felfelé és lefelé a portálon keresztül. Vertikális felskálázásával számára nagyobb feldolgozási feladatok szükség lehet további kapacitást szerezhet, vagy csökkenthetjük a fürt amikor nem használja azt. Fürt horizontális fel-kapcsolatos útmutatásért lásd: [kezelése a HDInsight-fürtök](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>A rendszer karbantartása

Operációsrendszer-javítások és egyéb frissítés alkalmazásához karbantartása munkaidőn kívül Linuxos virtuális gépeken az alapul szolgáló egy HDInsight-fürtön történik. Általában karbantartási történik hajnalban 3:30-kor (a virtuális gép a helyi ideje alapján) minden hétfőjén és csütörtökén. Frissítések oly módon, hogy azok ne befolyásolják a fürt több mint egy negyedév egyszerre történik.

Az átjárócsomópontokhoz redundáns, és nem minden adatcsomópontok érinti, mivel ez idő alatt futó feladatok lelassíthatják. Azonban azok továbbra is fusson befejezését. Bármilyen egyéni szoftver vagy a helyi adatok, amely rendelkezik a rendszer megőrzi ezeket a karbantartási események között, kivéve, ha egy végzetes hiba bekövetkezése, amely egy fürt Újraépítés igényel.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>A HDInsight IDE Machine Learning-szolgáltatások beállításai

A HDInsight-fürt Linux peremhálózati csomópont R-alapú elemzési alkotóelemeit zónáját. HDInsight újabb verzióiban az RStudio Server alapértelmezett telepítést biztosítanak az élcsomóponton, a böngésző alapú IDE. RStudio Server használatát a fejlesztési IDE és R-szkriptek végrehajtása jelentősen hatékonyabban dolgozhat, mint az R-konzol csak használatával is lehet.

Emellett telepíteni egy asztali IDE, és használhatja azokat a fürt eléréséhez az távoli MapReduce vagy a Spark számítási környezetek használatával. Lehetőségek a Microsoft [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), az RStudio és Walware céljai az Eclipse-alapú [StatET](http://www.walware.de/goto/statet).

Emellett hozzáférhet a peremhálózati csomópont R-konzol beírásával **R** keresztül az SSH- vagy putty-t kapcsolódás után Linux parancs parancssorba. Ha a konzol felhasználói felületén, célszerű a R-szkript fejlesztői egy szövegszerkesztőbe futtassa egy másik ablakban, és a Kivágás, és illessze be a szkript szakaszok az R-konzolról, igény szerint.

## <a name="pricing"></a>Díjszabás

Az árak egy Machine Learning-szolgáltatások HDInsight-fürthöz társított hasonlóan minden olyan HDInsight-fürt esetében a díjak a struktúrája. Azok a neve, az adatok és élcsomópontok egy magóra kicsivel igény szerinti hozzáadásával az alapul szolgáló virtuális gépek méretezési alapulnak. További információkért lásd: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>További lépések

Machine Learning-szolgáltatások HDInsight-fürtökön való használatával kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [Ismerkedés a Machine Learning-szolgáltatások HDInsight-fürt](r-server-get-started.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [A Machine Learning-szolgáltatások HDInsight-fürt tárolási lehetőségek](r-server-storage.md)

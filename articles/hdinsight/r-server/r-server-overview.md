---
title: A ML-szolgáltatások bemutatása az Azure HDInsight
description: Ismerje meg, hogyan hozhat létre alkalmazásokat big data elemzéshez a HDInsight ML-szolgáltatásainak használatával.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: bd9b9edde0f4c5e3bea6e31342298f2df5a2b5fe
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241872"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Mi az az Azure HDInsight a ML-szolgáltatások?

A Microsoft Machine Learning Server központi telepítési lehetőségként érhető el, amikor HDInsight-fürtöket hoz létre az Azure-ban. Az ezt a lehetőséget biztosító fürt típusának neve **ml szolgáltatás**. Ezzel a képességgel az adatszakértők, a statisztikusok és az R-programozók igény szerinti hozzáféréssel rendelkeznek a HDInsight-alapú elemzési módszerek méretezhető, elosztott módszereihez.

A HDInsight ML-szolgáltatásai az R-alapú elemzések legújabb képességeit biztosítják az Azure Blob vagy Data Lake Storage szolgáltatásba betöltött, szinte bármilyen méretű adatkészletek esetében. Mivel a ML Services-fürt nyílt forráskódú R-re épül, a létrehozott R-alapú alkalmazások a 8000-es és nyílt forráskódú R-csomagok bármelyikét kihasználhatják. A skálázás, a Microsoft big data Analytics-csomagjának rutinja is elérhető.

A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való kapcsolódáshoz és az R-parancsfájlok futtatásához. A peremhálózati csomópontok esetében lehetősége van a skálázás párhuzamosan elosztott funkcióinak futtatására a peremhálózati csomópont-kiszolgáló magjai között. Azokat a fürt csomópontjain is futtathatja, ha a méretezőt a Hadoop-Térkép használatával csökkenti vagy Apache Spark a számítási környezeteket.

Az elemzésből eredő modellek vagy előrejelzések a helyszíni használatra tölthetők le. Az Azure-ban máshol is üzembe helyezhetők, különösen [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net) [webszolgáltatással](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Ismerkedés a HDInsight által nyújtott ML-szolgáltatásokkal

Ha egy ML Services-fürtöt szeretne létrehozni az Azure HDInsight-ben, válassza ki a **ml-szolgáltatások** fürtjének típusát, amikor a Azure Portal használatával hoz létre egy HDInsight-fürtöt. A ML-szolgáltatások fürtjének típusa ML Servert tartalmaz a fürt adatcsomópontjain és egy peremhálózati csomóponton, amely kirakodási zónaként szolgál a ML szolgáltatások-alapú elemzésekhez. A fürt létrehozásával kapcsolatos útmutatásért lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) .

## <a name="why-choose-ml-services-in-hdinsight"></a>Miért érdemes a ML-szolgáltatásokat választani a HDInsight-ben?

A HDInsight ML-szolgáltatásai a következő előnyöket biztosítják:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>A Microsoft és a nyílt forráskódú AI-innováció

  A ML-szolgáltatások olyan nagy mértékben méretezhető, elosztott algoritmusokat tartalmaznak, mint például a [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), a [Revoscalepy csomagjai](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)és a [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , amelyek a fizikai memória méreténél nagyobb adatméreteken dolgozhatnak, és a különböző platformokon futnak elosztott mód. További információ a Microsoft egyéni [R-csomagjainak](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) és a termékben található [Python-csomagok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) gyűjteményéről.
  
  A ML-szolgáltatások a nyílt forráskódú Közösségtől (R, Python és AI-eszközkészlet) származó Microsoft-innovációkat és-hozzájárulásokat egyaránt egyetlen nagyvállalati szintű platformra épülnek. Bármely R vagy Python nyílt forráskódú gépi tanulási csomag a Microsoft tulajdonában lévő bármilyen üzleti innovációval párhuzamosan működhet.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Egyszerű, biztonságos és nagy léptékű operacionalizálási és felügyelet

  A hagyományos paradigmák és környezetek révén a vállalatok sok időt és erőfeszítést fektetnek a operacionalizálási irányába. Ennek eredményeképpen a költségek és a késések is megmaradnak, beleértve a modellek fordítási idejét, az ismétléseket, hogy érvényes és aktuális, szabályozási jóváhagyást és a operacionalizálási-on keresztül kezelhesse az engedélyeket.

  A ML-szolgáltatások nagyvállalati szintű [operacionalizálási](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)biztosítanak, a gépi tanulási modell befejezése után mindössze néhány kattintással létrehozzák a webszolgáltatási API-kat. Ezek a [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) a felhőben egy kiszolgálói rácson futnak, és az üzletági alkalmazásokkal is integrálhatók. A rugalmas rácsra való üzembe helyezés lehetősége lehetővé teszi, hogy zökkenőmentesen méretezhető legyen az üzleti igényeknek megfelelően, a Batch és a valós idejű pontozás esetében is. Útmutatásért lásd: [MŰKÖDŐVÉ tenni ml szolgáltatások a HDInsight-on](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>A HDInsight által nyújtott ML-szolgáltatások főbb jellemzői

A következő szolgáltatások a HDInsight ML-szolgáltatásaiban szerepelnek.

| Szolgáltatás kategóriája | Leírás |
|------------------|-------------|
| R-enabled | R- [csomagok](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) az r-ben írt megoldásokhoz, az r nyílt forráskódú eloszlásával és a parancsfájlok futtatására szolgáló futásidejű infrastruktúrával. |
| Python – engedélyezve | Python- [modulok](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) a Pythonban írt megoldásokhoz, a Python és a futásidejű infrastruktúra nyílt forrású eloszlásával a parancsfájlok futtatásához.
| [Előre betanított modellek](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | A vizuális elemzéshez és a szöveg hangulatának elemzéséhez, amely készen áll az Ön által megadott adatgyűjtésre. |
| [Üzembe helyezés és használat](r-server-operationalize.md) | Működővé tenni a kiszolgálót, és webszolgáltatásként helyezhet üzembe megoldásokat. |
| [Távoli végrehajtás](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Indítsa el a távoli munkameneteket a hálózaton található ML Services-fürtön az ügyfél munkaállomásán. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Adattárolási lehetőségek a HDInsight ML-szolgáltatásaihoz

A HDInsight-fürtök HDFS-fájlrendszerének alapértelmezett tárolója társítható egy Azure Storage-fiókhoz vagy egy Azure Data Lake Storagehoz is. Ezzel a társítással biztosítható, hogy az elemzés során a rendszer a fürt tárterületén lévő bármilyen adattal feltöltse az adattárolást, és az adatai még a fürt törlése után is elérhetők legyenek. Az adatátvitelt a kiválasztott tárolási lehetőséghez különböző eszközökkel kezelheti, beleértve a Storage-fiók és a [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogram portálon alapuló feltöltési létesítményét is.

Lehetősége van arra, hogy a fürt üzembe helyezési folyamata során engedélyezze a további blob-és adattárakhoz való hozzáférést a használatban lévő elsődleges tárterülettől függetlenül.  A több Storage-fiók használatával kapcsolatos további információkért lásd: [Az Azure Storage lehetőségei a HDINSIGHT ml-szolgáltatásaihoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) .

A peremhálózati csomóponton a [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) tárolási lehetőségként is használhatja. A Azure Files lehetővé teszi, hogy az Azure Storage-ban létrehozott fájlmegosztást a Linux fájlrendszerbe csatlakoztassa. További információ ezekről az adattárolási lehetőségekről a HDInsight-fürtön található ML-szolgáltatások esetében: [Az Azure Storage lehetőségei a HDINSIGHT ml-szolgáltatásaihoz](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>A ML-szolgáltatások peremhálózati csomópontjának elérése

A peremhálózati csomóponton böngésző használatával kapcsolódhat a Microsoft ML Serverhoz. Alapértelmezés szerint telepítve van a fürt létrehozásakor.  Az R-konzol eléréséhez a parancssorból SSH/Putty használatával is csatlakozhat a fürt Edge csomóponthoz.

## <a name="develop-and-run-r-scripts"></a>R-parancsfájlok fejlesztése és futtatása

Az Ön által létrehozott és futtatott R-szkriptek az 8000-es és a nyílt forráskódú R-csomagok bármelyikét használhatják a skálázhatósági könyvtárban elérhető párhuzamos és elosztott rutinok mellett. Általánosságban elmondható, hogy egy, a peremhálózati csomóponton található ML-szolgáltatásokkal futtatott szkript az adott csomóponton az R-tolmácson belül fut. A kivételek azok a lépések, amelyeknek a méretezési függvényt olyan számítási környezettel kell meghívni, amely a Hadoop Térkép csökkentése (RxHadoopMR) vagy a Spark (RxSpark) értékre van beállítva. Ebben az esetben a függvény elosztott módon fut a fürt azon adatai (Task) csomópontjain, amelyek a hivatkozott adatokhoz vannak társítva. A különböző számítási környezeti lehetőségekkel kapcsolatos további információkért lásd: [számítási környezeti beállítások a HDINSIGHT ml-szolgáltatásokhoz](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Modell üzembe helyezése

Az adatmodellezés befejezésekor a modell segítségével működővé tenni az Azure-ból vagy a helyszínen elérhető új adatokra vonatkozó előrejelzéseket. Ez a folyamat pontozásként ismert. A pontozás a HDInsight, Azure Machine Learning és a helyszínen is elvégezhető.

### <a name="score-in-hdinsight"></a>Pontszám a HDInsight

A HDInsight való pontozáshoz írjon egy R-függvényt, amely meghívja a modellt, hogy előrejelzéseket készítsen egy új, a Storage-fiókba betöltött adatfájlhoz. Ezután mentse vissza a jóslatokat a Storage-fiókba. Ezt a rutin igény szerint futtathatja a fürt peremhálózati csomópontján vagy egy ütemezett feladaton keresztül.

### <a name="score-in-azure-machine-learning-aml"></a>Pontszám Azure Machine Learning (pénzmosás)

A Azure Machine Learning használatának értékeléséhez használja a [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) néven ismert nyílt forráskódú Azure Machine learning R-csomagot a modell Azure-webszolgáltatásként való közzétételéhez. A kényelem érdekében ez a csomag előre telepítve van a peremhálózati csomóponton. Ezután használja a Azure Machine Learning található létesítményeket a webszolgáltatás felhasználói felületének létrehozásához, majd a pontozáshoz szükség szerint hívja meg a webszolgáltatást.

Ha ezt a beállítást választja, a webszolgáltatáshoz való használathoz a méretezési modell összes objektumát egyenértékű, nyílt forráskódú objektummodell-objektumokra kell konvertálnia. Az átalakításhoz használjon skálázhatósági kényszerítési funkciókat, például a `as.randomForest()` az Ensemble-alapú modellekhez.

### <a name="score-on-premises"></a>Helyszíni pontozás

Ha a modellt a modell létrehozása után szeretné kipróbálni, a modellt az R-ben szerializálhatja, letöltheti, deszerializálhatja, majd felhasználhatja az új adatértékek kiértékeléséhez. Az új adatforrások a HDInsight vagy a [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)használatával a [pontszámban](#score-in-hdinsight) korábban ismertetett módszer használatával adhatók meg.

## <a name="maintain-the-cluster"></a>A fürt karbantartása

### <a name="install-and-maintain-r-packages"></a>R-csomagok telepítése és karbantartása

Az r-csomagok többsége szükséges a peremhálózati csomóponton, mivel az R-szkriptek legtöbb lépése ott fut. Ha további R-csomagokat szeretne telepíteni a peremhálózati csomóponton, használhatja az R `install.packages()` metódusát.

Ha csak rutinokat használ a skálázhatósági könyvtárból a fürtben, általában nem kell további R-csomagokat telepítenie az adatcsomópontokra. Előfordulhat azonban, hogy további csomagokat kell használnia a **rxExec** vagy a **RxDataStep** végrehajtásának támogatásához az adatcsomópontokon.

Ilyen esetekben a további csomagok a fürt létrehozása után parancsfájl-művelettel is telepíthetők. További információ: [a ml-szolgáltatások kezelése a HDInsight-fürtben](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoop MapReduce módosítása

A fürt módosítható úgy, hogy megváltoztassa a MapReduce-feladatok futtatásakor a ML-szolgáltatások számára elérhető memória mennyiségét. Fürt módosításához használja a fürt Azure Portal paneljén elérhető Apache Ambari felhasználói felületet. A fürt Ambari felhasználói felületének elérésével kapcsolatos utasításokért lásd: [HDInsight-fürtök kezelése a Ambari webes felhasználói felületén](../hdinsight-hadoop-manage-ambari.md).

A ML-szolgáltatások számára elérhető memória mennyisége is módosítható a **RxHadoopMR** hívásában a következő módon Hadoop kapcsolók használatával:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Fürt méretezése

A HDInsight lévő meglévő ML-szolgáltatások fürtje a portálon felfelé vagy lefelé is méretezhető. A vertikális felskálázással megszerezheti azt a további kapacitást, amire szüksége lehet a nagyobb feldolgozási feladatokhoz, vagy ha üresjáratban van, akkor a fürt vissza is méretezhető. A fürtök méretezésével kapcsolatos utasításokért lásd: [HDInsight-fürtök kezelése](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>A szolgáltatás karbantartása

Az operációs rendszer javításait és az egyéb frissítéseket a HDInsight-fürtben futó Linux virtuális gépeken, munkaidőn kívül hajtják végre. A karbantartást jellemzően a 3:30-kor (a virtuális gép helyi ideje alapján) végezheti el hétfőn és csütörtökön. A frissítések olyan módon történnek, hogy egyszerre nem befolyásolják a fürt egynegyedét.

Mivel a fő csomópontok redundánsak, és nem minden adatcsomópont érintett, az ebben az időszakban futó feladatok lelassulnak. Azonban a befejezéshez továbbra is futniuk kell. Minden olyan egyéni szoftver vagy helyi adatbázis megmarad ezen karbantartási események között, kivéve, ha a fürt újraépítését igénylő katasztrofális hiba történik.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-beállítások a HDInsight ML-szolgáltatásaihoz

A HDInsight-fürt Linux Edge csomópontja az R-alapú elemzések kirakodási zónája. A HDInsight legújabb verziói a RStudio-kiszolgáló alapértelmezett telepítését teszik lehetővé a peremhálózati csomóponton böngészőalapú IDE-ként. Az r-szkriptek fejlesztéséhez és végrehajtásához az RStudio-kiszolgáló IDE-ként való használata jelentősen termelékenyebb lehet, mint az R-konzol használata.

Emellett telepíthet egy asztali IDE-t, és használhatja a fürt eléréséhez egy távoli MapReduce vagy Spark számítási környezet használatával. A lehetőségek közé tartozik [a Microsoft R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), a RStudio és a Walware Eclipse-alapú [StatET](http://www.walware.de/goto/statet).

Emellett az r-konzolt a peremhálózati csomóponton is elérheti, ha az SSH vagy a PuTTY használatával történő csatlakozás után az **r** parancsot írja be a Linux-parancssorba. A konzol felületének használatakor érdemes egy szövegszerkesztőt futtatni az R-szkriptek fejlesztéséhez egy másik ablakban, és szükség szerint ki-és beillesztheti a szkript részét az R-konzolra.

## <a name="pricing"></a>Díjszabás

A ML-szolgáltatások HDInsight-fürthöz társított árak a más HDInsight-fürtök áraihoz hasonlóan vannak strukturálva. Ezek a mögöttes virtuális gépek méretén alapulnak a név, az adat és a peremhálózati csomópontok között, és egy fő órás felemelkedést is igénybe vesznek. További információ: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a HDInsight-fürtökön található ML-szolgáltatások használatáról, tekintse meg a következő témaköröket:

* [R-szkript végrehajtása egy ML Services-fürtön az Azure HDInsight az RStudio-kiszolgáló használatával](machine-learning-services-quickstart-job-rstudio.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [A ML Services-fürt tárolási beállításai a HDInsight](r-server-storage.md)

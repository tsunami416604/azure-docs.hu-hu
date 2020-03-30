---
title: Mi az Azure Machine Learning
description: Az Azure Machine Learning áttekintése – Integrált, végpontok között adatelemzési megoldás professzionális adatszakértők számára a fejlett elemzési alkalmazások felhőszintű fejlesztéséhez, kísérletezéséhez és üzembe helyezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: b8dbbb2810277bef20cb3b9b47a63deeea3e0ff9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241611"
---
# <a name="what-is-azure-machine-learning"></a>Mi az Azure Machine Learning?

Ebben a cikkben megismerheti az Azure Machine Learning felhőalapú környezetet, amelynek segítségével betaníthatja, üzembe helyezheti, automatizálhatja, kezelheti és nyomon követheti az ml-modelleket. 

Az Azure Machine Learning bármilyen típusú gépi tanuláshoz használható, a klasszikus ml-től a mélytanulásig, felügyelt és felügyelet nélküli tanulásig. Akár Python- vagy R-kódot, akár nulla kód-/alacsony kódú beállításokat szeretne írni, például a [tervezőt,](tutorial-designer-automobile-price-train-score.md)létrehozhat, betaníthat és nyomon követheti a rendkívül pontos gépi tanulási és mélytanulási modelleket az Azure Machine Learning Workspace-ben. 

Kezdje el a képzést a helyi gépen, majd horizontális felskálázása a felhőbe. 

A szolgáltatás együttműködik a népszerű nyílt forráskódú eszközökkel is, mint például a PyTorch, a TensorFlow és a scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Ingyenes próbaverzió!**  Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt elkezdené. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree) Azure-szolgáltatásokra elkölthető krediteket kap. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.


## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás funkció?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulás használatával a számítógépek anélkül tanulnak, hogy kifejezetten programoznák őket.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárol, a gépi tanulás segít más termékeket ajánlani, amelyeket a megvásárolt termékek alapján szeretne. Vagy például a bankkártya lehúzásakor a gépi tanulás összeveti az adott tranzakciót az adatbázisában található tranzakciókkal, így segít a csalások felismerésében. Ha robotporszívóra bízza a szoba kitakarítását, a gépi tanulás segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="machine-learning-tools-to-fit-each-task"></a>Gépi tanulási eszközök az egyes feladatokhoz 

Az Azure Machine Learning biztosítja az összes olyan eszközt, amelyet a fejlesztőknek és adatszakértőknek szükségük van a gépi tanulási munkafolyamataikhoz, többek között a következőket:
+ Az [Azure Machine Learning tervezője](tutorial-designer-automobile-price-train-score.md) (előzetes verzió): drag-n-drop modulok a kísérletek létrehozásához, majd a folyamatok üzembe helyezéséhez.

+ Jupyter-jegyzetfüzetek: használja [a példa notebookok,](https://aka.ms/aml-notebooks) vagy hozzon létre saját jegyzetfüzeteket, hogy kihasználja az <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK Python-minták</a> a gépi tanuláshoz. 

+ R-parancsfájlok vagy notebookok, amelyekben az <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK for R</a> segítségével írja meg saját kódját, vagy használja az R modulokat a tervezőben.

+ [Visual Studio Code-bővítmény](tutorial-setup-vscode-extension.md)

+ [Machine learning CLI](reference-azure-machine-learning-cli.md)

+ Nyílt forráskódú keretrendszerek, mint például a PyTorch, a TensorFlow és a scikit-learn és még sok más

Az [MLflow segítségével metrikákat is nyomon követhet, modelleket](how-to-use-mlflow.md) vagy Kubeflow-t [helyezhet el teljes körű munkafolyamat-folyamatok létrehozásához.](https://www.kubeflow.org/docs/azure/)

## <a name="build-ml-models-in-python-or-r"></a>Ml-modellek készítése Pythonban vagy R-ben

Az Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> vagy az <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>használatával kezdje el a betanítást a helyi gépen. Ezután a felhőre skálázhatja. 

Számos rendelkezésre álló [számítási cél ,](how-to-set-up-training-targets.md)például az Azure Machine Learning Compute és az Azure [Databricks](/azure/azure-databricks/what-is-azure-databricks), valamint a [fejlett hiperparaméter-hangolási szolgáltatások,](how-to-tune-hyperparameters.md)a felhő segítségével gyorsabban hozhat létre jobb modelleket.

A [modellbetanítást és -hangolást](tutorial-auto-train-models.md) az SDK használatával is automatizálhatja.

## <a name="build-ml-models-with-no-code-tools"></a>Ml-modellek készítése kódmentes eszközökkel

A kód nélküli vagy alacsony kódú betanításhoz és telepítéshez próbálkozzon a következőkkal:

+ **Azure Machine Learning-tervező (előzetes verzió)**

  A tervező segítségével előkészítheti az adatokat, betaníthat, tesztelhet, üzembe helyezhet, kezelhet és nyomon követheti a gépi tanulási modelleket kód írása nélkül. Nincs szükség programozásra, vizuálisan csatlakoztathatja az adatkészleteket és a modulokat a modell létrehozásához. Próbálja ki a [tervező bemutató](tutorial-designer-automobile-price-train-score.md).

  További információ [az Azure Machine Learning tervezőjének áttekintése című cikkben.](concept-designer.md) 

  ![Példa az Azure Machine Learning tervezőjének](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Automatizált gépi tanulási felhasználói felület**

  Ismerje meg, hogyan hozhat létre [automatizált gépelési kísérleteket](tutorial-first-experiment-automated-ml.md) a könnyen használható felületen. 

  [![Az Azure Machine Learning stúdió navigációs ablaktáblája](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: & életciklus-kezelés üzembe helyezése
Ha a megfelelő modellel rendelkezik, egyszerűen használhatja azt egy webszolgáltatásban, egy IoT-eszközön vagy a Power BI-ból. További információt a [telepítésről és](how-to-deploy-and-where.md)a helyről szóló cikkben talál.

Ezután kezelheti az üzembe helyezett modelleket az [Azure Machine Learning SDK pythonhoz](https://aka.ms/aml-sdk), [az Azure Machine Learning stúdióhoz](https://ml.azure.com)vagy a [gépi tanulási CLI](reference-azure-machine-learning-cli.md)használatával.

Ezek a modellek lehet használni, és adja vissza előrejelzések [valós időben,](how-to-consume-web-service.md) vagy [aszinkron](how-to-use-parallel-run-step.md) nagy mennyiségű adatot.

A fejlett [gépi tanulási folyamatokkal](concept-ml-pipelines.md)pedig az adatok előkészítésétől, a modellbetanítástól és a kiértékeléstől az üzembe helyezésig minden lépésnél együttműködhet. A folyamatok lehetővé teszik a következőket:

* Automatizálja a végpontok között a gépi tanulási folyamatot a felhőben
* Az összetevők újrafelhasználása, és csak szükség esetén futtassa újra a lépéseket
* Különböző számítási erőforrások használata minden lépésben
* Kötegelt pontozási feladatok futtatása

Ha parancsfájlok használatával automatizálja a gépi tanulási munkafolyamatot, a [gépi tanulási CLI](reference-azure-machine-learning-cli.md) parancssori eszközöket biztosít, amelyek gyakori feladatokat hajtanak végre, például betanítási futtatást vagy modellt telepítenek.

Az Azure Machine Learning használatának megkezdéséhez olvassa el a [Következő lépések .](#next-steps)

## <a name="integration-with-other-services"></a>Integráció más szolgáltatásokkal

Az Azure Machine Learning az Azure platformon más szolgáltatásokkal is együttműködik, és olyan nyílt forráskódú eszközökkel is integrálható, mint a Git és az MLFlow.

+ Számítási célok, például __az Azure Kubernetes szolgáltatás,__ __az Azure Container Instances, az__Azure __Databricks, az__Azure Data Lake __Analytics__és az __Azure HDInsight.__ A számítási célokról a [Mik azok a számítási célok?](concept-compute-target.md)
+ __Az Azure Event Grid__. További információ: [Consume Azure Machine Learning events.](concept-event-grid-integration.md)
+ __Az Azure Monitor__. További információ: [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).
+ Az olyan adattárak, mint az __Azure Storage-fiókok__, __az Azure Data Lake Storage,__ __az Azure SQL Database,__ __az Azure Database for PostgreSQL__és __az Azure Open Datasets__. További információ: [Access data in Azure storage services](how-to-access-data.md) and Create [datasets with Azure Open Datasets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Az Azure virtuális hálózatok__. További információ: [Biztonságos kísérletezés és következtetés a virtuális hálózatban.](how-to-enable-virtual-network.md)
+ __Az Azure-folyamatok__. További információ: [Gépi tanulási modellek betanítása és üzembe helyezése.](/azure/devops/pipelines/targets/azure-machine-learning)
+ __Git tárház naplók__. További információ: [Git integration](concept-train-model-git-integration.md).
+ __MLFlow .__ További információ: [MLflow a metrikák nyomon követéséhez és modellek üzembe helyezéséhez](how-to-use-mlflow.md) 
+ __Kubeflow__. További információt a [teljes körű munkafolyamat-folyamatok létrehozásácímű témakörben talál.](https://www.kubeflow.org/docs/azure/)

### <a name="secure-communications"></a>Biztonságos kommunikáció

Az Azure Storage-fiók, számítási célok és egyéb erőforrások biztonságosan használhatók egy virtuális hálózaton belül a modellek betanításához és a következtetések végrehajtásához. További információ: [Biztonságos kísérletezés és következtetés a virtuális hálózatban.](how-to-enable-virtual-network.md)

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Alapszintű & Enterprise kiadások

Az Azure Machine Learning két, a gépi tanulási igényekre szabott kiadást kínál:
+ Alapszintű (általánosan elérhető)
+ Nagyvállalati (előzetes verzió)

Ezek a kiadások határozzák meg, hogy mely gépi tanulási eszközök érhetők el a fejlesztők és az adatszakértők számára a munkaterületükről.   

Az alapvető munkaterületek lehetővé teszik az Azure Machine Learning további használatát, és csak a gépi tanulási folyamat során felhasznált Azure-erőforrásokért kell fizetnie. Az Enterprise edition munkaterületek csak az Azure-felhasználásért kerülnek felszámolásra, amíg a kiadás előzetes verzióban érhető el. További információ az Azure Machine Learning-kiadás áttekintéséről [& díjszabási lapon.](https://azure.microsoft.com/pricing/details/machine-learning/) 

A kiadást mindig hozzárendeli, amikor munkaterületet hoz létre. A már meglévő munkaterületek pedig alapszintű kiadássá lettek konvertálva. Az alapkiadás tartalmazza az összes olyan funkciót, amely már 2019 októberében általánosan elérhető volt. Az Enterprise Edition szolgáltatásaival készített munkaterületeken végzett kísérletek továbbra is csak olvashatóhelyen lesznek elérhetők, amíg nem frissít az Enterprise-ra. Megtudhatja, hogy [miként frissíthet alapmunkaterületet Enterprise Edition kiadásra.](how-to-manage-workspace.md#upgrade) 

Az ügyfelek felelősek a számítási és egyéb Azure-erőforrásokkal kapcsolatban ez idő alatt felmerülő költségekért.

## <a name="next-steps"></a>További lépések

- Hozza létre az első kísérletet a kívánt módszerrel:
  + [Python-jegyzetfüzetek használata & ML-modellek üzembe helyezésének betanítása](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata & ml-modellek telepítésének betanításához](tutorial-1st-r-experiment.md) 
  + [Automatikus gépi tanulás használata az & üzembe helyezéséhez használt gépi tanulási modellek betanításához](tutorial-first-experiment-automated-ml.md) 
  + [A tervező húzási & lehúzási képességeivel betanítsa & üzembe helyezését](tutorial-designer-automobile-price-train-score.md) 
  + [A gépi tanulási CLI segítségével modellbe tanítható és helyezhet üzembe](tutorial-train-deploy-model-cli.md)

- Ismerje meg a [gépi tanulási folyamatokat](concept-ml-pipelines.md) a gépi tanulási forgatókönyvek létrehozásához, optimalizálásához és felügyeletéhez.

- Olvassa el a részletes [Azure Machine Learning-architektúra és fogalmak cikket.](concept-azure-machine-learning-architecture.md)

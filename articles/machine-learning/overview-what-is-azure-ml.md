---
title: Mi az Azure Machine Learning?
description: Az Azure Machine Learning áttekintése – integrált, teljes körű adatelemzési megoldás professzionális adatszakértők számára a fejlett analitikai alkalmazások fejlesztéséhez, kipróbálásához és üzembe helyezéséhez Felhőbeli méretezéssel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: 53ccd610c6f0f36d4ab04efbada2070a22975023
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650111"
---
# <a name="what-is-azure-machine-learning"></a>Mi az Azure Machine Learning?

Ebből a cikkből megAzure Machine Learning tudhatja, hogyan használhatók a felhőalapú környezetek, amelyek segítségével betaníthatja, üzembe helyezheti, automatizálhatja, kezelheti és nyomon követheti a ML-modelleket. 

Azure Machine Learning bármilyen gépi tanuláshoz használható, klasszikus ml-ből mély tanulásra, felügyelt és nem felügyelt tanulásra. Akár Python, akár R-kód, akár nulla-kód/alacsony kódú lehetőség (például a [tervező](tutorial-designer-automobile-price-train-score.md)) helyett szeretne írni, a nagyvállalati tanulási és a részletes tanulási modellek kiépítését, betanítását és nyomon követését is elvégezheti egy Azure Machine learning-munkaterület. 

Indítsa el a képzést a helyi gépen, majd bővítse a felhőt. 

A szolgáltatás emellett együttműködik a népszerű mély tanulási és megerősítő nyílt forráskódú eszközökkel, például a PyTorch, a TensorFlow, a scikit-Learn és a Ray RLlib. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Ingyenes próbaverzió!**  Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma. Azure-szolgáltatásokra elkölthető krediteket kap. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.


## <a name="what-is-machine-learning"></a>Mi az a gépi tanulás?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulás használatával a számítógépek külön programozás nélkül tanulnak.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárol, a gépi tanulás segítséget nyújt más, a vásárolt termékek alapján. Vagy ha ellopják a hitelkártyáját, a gépi tanulás összeveti a tranzakciót egy tranzakció-adatbázissal, és segít a csalás észlelésében. Ha robotporszívóra bízza a szoba kitakarítását, a gépi tanulás segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="machine-learning-tools-to-fit-each-task"></a>Gépi tanulási eszközök az egyes feladatokhoz 

Azure Machine Learning biztosítja a gépi tanulási munkafolyamataihoz szükséges összes eszközt a fejlesztők és az adatszakértők számára, beleértve a következőket:
+ A [Azure Machine learning Designer](tutorial-designer-automobile-price-train-score.md) (előzetes verzió): húzzon-n-drop modulokat a kísérletek létrehozásához, majd a folyamatok üzembe helyezéséhez.

+ Jupyter jegyzetfüzetek: a [példánkban szereplő jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks) használatával vagy saját jegyzetfüzetek létrehozásával kihasználhatja a gépi tanuláshoz készült SDK-t a <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python</a> -mintákhoz. 

+ R-parancsfájlok vagy jegyzetfüzetek, amelyekben a saját kód írásához használja az <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK</a> -t, vagy használja a tervező R modulját.

+ + A [számos Modelles megoldás-gyorsító](https://aka.ms/many-models) (előzetes verzió) a Azure Machine Learningra épít, és lehetővé teszi több száz vagy akár több ezer gépi tanulási modell betanítását, üzemeltetését és felügyeletét.

+ [Visual Studio Code-bővítmény](tutorial-setup-vscode-extension.md)

+ [Machine learning CLI](reference-azure-machine-learning-cli.md)

+ Nyílt forráskódú keretrendszerek, mint például a PyTorch, a TensorFlow és a scikit – Learn és sok más

+ [Megerősítő tanulás](how-to-use-reinforcement-learning.md) a Ray RLlib

A [MLflow használatával nyomon követheti a metrikákat, és üzembe helyezheti a modelleket](how-to-use-mlflow.md) vagy a Kubeflow, így teljes [körű munkafolyamat-folyamatokat hozhat létre](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>ML modellek készítése a Pythonban vagy az R-ben

Indítsa el a képzést a helyi gépen a Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">PYTHON SDK</a> vagy az <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>használatával. Ezután kibővítheti a felhőt. 

Számos elérhető [számítási cél](how-to-set-up-training-targets.md), például a Azure Machine learning számítási és [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), valamint a [fejlett hiperparaméter-hangolási szolgáltatások](how-to-tune-hyperparameters.md)révén a felhő hatékonyságával gyorsabban hozhat létre jobb modelleket.

Az SDK segítségével [automatizálhatja a modell betanítását és finomhangolását](tutorial-auto-train-models.md) is.

## <a name="build-ml-models-with-no-code-tools"></a>A kód nélküli eszközökkel rendelkező ML-modellek készítése

A kód nélküli vagy alacsony kódú képzés és üzembe helyezés esetén próbálkozzon a következő lépésekkel:

+ **Azure Machine Learning Designer (előzetes verzió)**

  A Designer segítségével előkészítheti a gépi tanulási modelleket, betaníthatja, üzembe helyezheti, kezelheti és nyomon követheti, anélkül, hogy kódot kellene írnia. Nincs szükség programozásra, vizuálisan összekapcsolhatók az adatkészletek és modulok a modell létrehozásához. Próbálja ki a [tervezői oktatóanyagot](tutorial-designer-automobile-price-train-score.md).

  További információt [a Azure Machine learning Designer áttekintő cikkében](concept-designer.md)talál. 

  ![Azure Machine Learning Designer – példa](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Automatikus gépi tanulás felhasználói felülete**

  Megtudhatja, hogyan hozhat létre [AUTOMATIZÁLT ml-kísérleteket](tutorial-first-experiment-automated-ml.md) a könnyen használható felületen. 

  [![Azure Machine Learning Studio navigációs panelje](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: & életciklus-felügyelet üzembe helyezése
Ha rendelkezik a megfelelő modellel, egyszerűen használhatja egy webszolgáltatásban, egy IoT-eszközön vagy Power BI. További információ: a [telepítésének és helyének](how-to-deploy-and-where.md)ismertetése.

Ezután felügyelheti a telepített modelleket a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)-val, a [Azure Machine learning Studióval](https://ml.azure.com)vagy a [Machine learning parancssori](reference-azure-machine-learning-cli.md)felülettel.

Ezeket a modelleket felhasználhatja, és [valós időben](how-to-consume-web-service.md) vagy [aszinkron módon](how-to-use-parallel-run-step.md) , nagy mennyiségű adattal lehet visszaadni az előrejelzéseket.

A fejlett [gépi tanulási folyamatokkal](concept-ml-pipelines.md)pedig az üzembe helyezés során az adatok előkészítése, a modell betanítása és a kiértékelés egyes lépésein is dolgozhat. A folyamatok a következőket teszik lehetővé:

* A teljes körű gépi tanulási folyamat automatizálása a felhőben
* Összetevők újrafelhasználása, és csak szükség esetén futtassa újra a lépéseket
* Különböző számítási erőforrások használata az egyes lépésekben
* Batch-pontozási feladatok futtatása

Ha parancsfájlokkal szeretné automatizálni a gépi tanulási munkafolyamatot, a [Machine learning CLI](reference-azure-machine-learning-cli.md) olyan parancssori eszközöket biztosít, amelyek gyakori feladatokat hajtanak végre, például beküldenek egy képzést, vagy egy modellt telepítenek.

A Azure Machine Learning használatának megkezdéséhez tekintse meg a [következő lépéseket](#next-steps).

## <a name="integration-with-other-services"></a>Integráció más szolgáltatásokkal

A Azure Machine Learning együttműködik az Azure platform egyéb szolgáltatásaival, és olyan nyílt forráskódú eszközökkel is integrálható, mint a git és a MLFlow.

+ Számítási célok, például __Azure Kubernetes szolgáltatás__, __Azure Container instances__, __Azure Databricks__, __Azure Data Lake Analytics__és az __Azure HDInsight__. A számítási célokkal kapcsolatos további információkért lásd: [Mik a számítási célok?](concept-compute-target.md).
+ __Azure Event Grid__. További információ: [Azure Machine learning események felhasználása](concept-event-grid-integration.md).
+ __Azure monitor__. További információ: [Monitoring Azure Machine learning](monitor-azure-machine-learning.md).
+ Olyan adattárakat, mint például az __Azure Storage-fiókok__, a __Azure Data Lake Storage__, a __Azure SQL Database__, a __Azure Database for PostgreSQL__és az __Azure Open-adatkészletek__. További információ: az [Azure Storage szolgáltatásokban tárolt adatok elérése](how-to-access-data.md) és [adatkészletek létrehozása az Azure Open adatkészletekkel](how-to-create-register-datasets.md).
+ __Azure-beli virtuális hálózatok__. További információ: [biztonságos kísérletezés és következtetés egy virtuális hálózaton](how-to-enable-virtual-network.md).
+ __Azure-folyamatok__. További információ: a [gépi tanulási modellek betanítása és üzembe helyezése](/azure/devops/pipelines/targets/azure-machine-learning).
+ A __git-tárház naplói__. További információ: git- [integráció](concept-train-model-git-integration.md).
+ __MLFlow__. További információ: [MLflow a mérőszámok nyomon követéséhez és modellek üzembe helyezéséhez](how-to-use-mlflow.md) 
+ __Kubeflow__. További információ: [végpontok közötti munkafolyamat-folyamatok létrehozása](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Biztonságos kommunikáció

Az Azure Storage-fiók, a számítási célok és az egyéb erőforrások biztonságosan használhatók a virtuális hálózaton belül a modellek betanításához és következtetések teljesítéséhez. További információ: [biztonságos kísérletezés és következtetés egy virtuális hálózaton](how-to-enable-virtual-network.md).

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Alapszintű & Enterprise kiadás

Azure Machine Learning két, a gépi tanulási igényekhez igazított kiadást kínál:
+ **Alapszintű**: kiváló a nyílt forráskódú fejlesztéshez a Cloud Scale-ben a Code-First felülettel.

+ **Enterprise**: az összes alapszintű, valamint egy webes felület (Studio) + biztonságos, átfogó ml életciklus-kezelés minden képzettségi szinten.

Ha többet szeretne megtudni ezekről a kiadásokról és a feloldható képességekről, olvassa el a ["Azure Machine learning Editions" című cikket](concept-editions.md).

## <a name="next-steps"></a>További lépések

- Hozza létre első kísérletét a kívánt módszerrel:
  + [Python-jegyzetfüzetek használata & ML-modellek üzembe helyezéséhez](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata & ML-modellek üzembe helyezéséhez](tutorial-1st-r-experiment.md) 
  + [Az automatizált gépi tanulás használata & ML-modellek üzembe helyezéséhez](tutorial-first-experiment-automated-ml.md) 
  + [A tervező drag & drop funkciói a & üzembe helyezéséhez](tutorial-designer-automobile-price-train-score.md) 
  + [Modellek betanítása és üzembe helyezése a Machine learning parancssori felület használatával](tutorial-train-deploy-model-cli.md)

- Ismerje meg a [gépi tanulási folyamatokat](concept-ml-pipelines.md) a gépi tanulási forgatókönyvek létrehozásához, optimalizálásához és felügyeletéhez.

- Olvassa el a részletes [Azure Machine learning architektúrát és fogalmakat](concept-azure-machine-learning-architecture.md) ismertető cikket.

---
title: Mi az Azure Machine Learning?
description: Az Azure Machine Learning áttekintése – integrált, teljes körű adatelemzési megoldás professzionális adatszakértők számára a fejlett analitikai alkalmazások fejlesztéséhez, kipróbálásához és üzembe helyezéséhez Felhőbeli méretezéssel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: c845966c86659c0ff983bf33c492a67dd99275f0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692934"
---
# <a name="what-is-azure-machine-learning"></a>Mi az Azure Machine Learning?

A Azure Machine Learning egy felhőalapú szolgáltatás, amely a gépi tanulási modellek betanítására, üzembe helyezésére, automatizálására és felügyeletére használható, mindezt a felhő által biztosított széles skálán.

## <a name="what-is-machine-learning"></a>Mi a gépi tanulás?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulás használatával a számítógépek külön programozás nélkül tanulnak.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárol, a gépi tanulás segítséget nyújt más, a vásárolt termékek alapján. Vagy például a bankkártya lehúzásakor a gépi tanulás összeveti az adott tranzakciót az adatbázisában található tranzakciókkal, így segít a csalások felismerésében. Ha robotporszívóra bízza a szoba kitakarítását, a gépi tanulás segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="what-is-azure-machine-learning"></a>Mi az Azure Machine Learning?

A Azure Machine Learning felhőalapú környezetet biztosít, amely lehetővé teszi az adatelőkészítést, a betanítást, a tesztelést, az üzembe helyezést, a felügyeletet és nyomon követni a gépi tanulási modelleket. Indítsa el a képzést a helyi gépen, majd bővítse a felhőt. A szolgáltatás teljes mértékben támogatja a nyílt forráskódú technológiákat, például a PyTorch, a TensorFlow és a scikit, és bármilyen gépi tanuláshoz használható, klasszikus ml-ből mély tanulásra, felügyelt és nem felügyelt tanulásra.

Felderítheti és előkészítheti az adatfeldolgozási és-tesztelési modelleket, és számos olyan eszközt alkalmazhat, mint például a:
+ Olyan [vizualizációs felület](ui-tutorial-automobile-price-train-score.md) , amelyben az n-drop modulok használatával felépítheti a kísérleteket, majd üzembe helyezheti a modelleket
+ [Jupyter jegyzetfüzetek](https://jupyter.org) , amelyekben az [SDK](https://docs.microsoft.com/azure/machine-learning) -k segítségével saját kódokat írhat, például a [minta jegyzetfüzeteket](https://aka.ms/aml-notebooks)
+ [Visual Studio Code-bővítmény](how-to-vscode-tools.md)


> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Mire használhatom az Azure Machine Learning szolgáltatást?

A <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Azure Machine learning PYTHON SDK</a> -t nyílt forráskódú Python-csomagokkal használhatja, vagy a [Visual Interface (előzetes verzió)](ui-tutorial-automobile-price-train-score.md) használatával magas szintű gépi tanulást és mélyreható tanulási modelleket építhet ki és taníthat ki egy Azure Machine learning-szolgáltatásban Munkaterület.

A nyílt forráskódú Python-csomagokban elérhető számos gépi tanulási összetevő közül választhat, például a <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-Learn</a>, a <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, a <a href="https://pytorch.org" target="_blank">PyTorch</a>és a <a href="https://mxnet.io" target="_blank">MXNet</a>.

Akár kódot ír, akár a vizualizáció felületét használja, több futtatást is nyomon követheti a legjobb megoldás megtalálásához, valamint az üzembe helyezett modellek kezeléséhez.

### <a name="code-first-experience"></a>Kód – első élmény

Indítsa el a képzést a helyi gépen a <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Azure Machine learning PYTHON SDK</a> használatával, majd a felhőbe. Számos elérhető [számítási cél](how-to-set-up-training-targets.md), például a Azure Machine learning számítási és [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), valamint a [fejlett hiperparaméter-hangolási szolgáltatások](how-to-tune-hyperparameters.md)révén a felhő hatékonyságával gyorsabban hozhat létre jobb modelleket.

Az SDK segítségével [automatizálhatja a modell betanítását és finomhangolását](tutorial-auto-train-models.md) is.

### <a name="ui-based-low-code-experience"></a>Felhasználói felület-alapú, alacsony kódú élmény

A kód nélküli képzéshez és üzembe helyezéshez próbálkozzon az alábbiakkal:

+ [AUTOMATIZÁLT ml-kísérletek](tutorial-first-experiment-automated-ml.md) létrehozása a könnyen használható felületen.
+ A [drag-n-drop kísérletezés a vizualizációs felületen](ui-tutorial-automobile-price-train-score.md).
  Azure Machine Learning ![Visual felülete ](media/overview-what-is-azure-ml/visual-interface.png)



### <a name="operationalization-mlops"></a>Operacionalizálási (MLOps)

Ha rendelkezik a megfelelő modellel, egyszerűen használhatja egy webszolgáltatásban, egy IoT-eszközön vagy Power BI. További információ: a [telepítésének és helyének](how-to-deploy-and-where.md)ismertetése.

Ezután felügyelheti a telepített modelleket a [Pythonhoz készült Azure Machine learning SDK](https://aka.ms/aml-sdk), a [Azure Portal](https://portal.azure.com/) vagy a munkaterület kezdőlapja [(előzetes verzió)](https://ml.azure.com)használatával.

Ezeket a modelleket felhasználhatja, és [valós időben](how-to-consume-web-service.md) vagy [aszinkron módon](how-to-run-batch-predictions.md) , nagy mennyiségű adattal lehet visszaadni az előrejelzéseket.

A fejlett [gépi tanulási folyamatokkal](concept-ml-pipelines.md)pedig az üzembe helyezés során az adatok előkészítése, a modell betanítása és a kiértékelés egyes lépésein is dolgozhat. A folyamatok a következőket teszik lehetővé:

* A teljes körű gépi tanulási folyamat automatizálása a felhőben
* Összetevők újbóli felhasználása, és szükség esetén csak a lépések újrafuttatása
* Különböző számítási erőforrások használata az egyes lépésekben
* Batch-pontozási feladatok futtatása

A Azure Machine Learning használatának megkezdéséhez tekintse meg a [következő lépéseket](#next-steps).

## <a name="how-does-azure-machine-learning-differ-from-studio"></a>Miben különbözik Azure Machine Learning a Studiótól?

A [Machine learning Studio](../studio/what-is-ml-studio.md) egy együttműködésen alapuló, húzással ellátható vizualizációs munkaterület, amely képes gépi tanulási megoldások létrehozására, tesztelésére és üzembe helyezésére anélkül, hogy kódot kellene írnia. Előre összeépített és előre konfigurált gépi tanulási algoritmusokat és adatkezelési modulokat, valamint egy szabadalmaztatott számítási platformot használ.

A Azure Machine Learning az SDK **-k és a-** a vizualizációs felületet (előzetes verzió) is biztosítja az adatelőkészítés, a gépi tanulási modellek betanítása és üzembe helyezése során. Ez a vizuális felület (előzetes verzió) hasonló húzási felületet biztosít a studióhoz. A Studio tulajdonosi számítási platformjának eltérően azonban a vizualizációs felület saját számítási erőforrásokat használ, és teljes mértékben integrálva van Azure Machine Learningba.

Íme egy gyors összehasonlítás.

|| Machine Learning Studio | Azure Machine Learning:<br/>Vizuális felület|
|---| --- | --- |
|| Általánosan elérhető (GA) | Előzetes verzióban|
|Húzási interfész| Igen | Igen|
|Kísérlet| Scale (10GB betanítási adatkorlátja) | Méretezés számítási céllal|
|Illesztőfelületi modulok| Számos | Népszerű modulok kezdeti készlete|
|Számítási célok betanítása| Tulajdonosi számítási cél, csak CPU|PÉNZMOSÁS-számítás (GPU/CPU)<br/> Notebook virtuális gépek |
|Viszonyítási számítási célok| Tulajdonosi webszolgáltatás formátuma, nem testreszabható | Azure Kubernetes szolgáltatás (valós idejű következtetés) <br/>PÉNZMOSÁS-számítás (batch-következtetés) |
|ML-folyamat| Nem támogatott | Folyamat létrehozása <br/> közzétett folyamat <br/> Folyamat végpontja <br/> [További információ az ML-folyamatról](concept-ml-pipelines.md)|
|ML-Ops| Alapszintű modell kezelése és üzembe helyezése | Konfigurálható üzembe helyezés, modell és folyamat verziószámozása|
|Modell| Tulajdonosi formátum. Nem használható a studión kívül | A standard formátum, amely a betanítási feladatoktól függ|
|Automatizált modellek betanítása és hiperparaméter finomhangolása | Nem | Még nem a Visual Interface-ben. <br/> (A Python SDK és a munkaterület kezdőlapján támogatott.) |

Próbálja ki a vizualizációs felületet (előzetes verzió) az [oktatóanyaggal: az autó árának előrejelzése a vizualizációs felületen](ui-tutorial-automobile-price-train-score.md).

> [!NOTE]
> A Studióban létrehozott modellek nem helyezhetők üzembe vagy nem kezelhetők Azure Machine Learning. A szolgáltatás vizualizációs felületén létrehozott és üzembe helyezett modellek azonban a Azure Machine Learning munkaterületen kezelhetők.

## <a name="free-trial"></a>Ingyenes próbalehetőség

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

Azure-szolgáltatásokra elkölthető krediteket kap. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez. Vagy [aktiválhatja az MSDN előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), amelyek havi kreditet biztosítanak a fizetős Azure-szolgáltatásokhoz.

## <a name="next-steps"></a>Következő lépések

- A kezdéshez [hozzon létre egy Machine learning szolgáltatás-munkaterületet](how-to-manage-workspace.md) .

- Kövesse a teljes hosszúságú oktatóanyagokat:
  + [Munkaterület létrehozása és az első ML-modell betanítása](tutorial-1st-experiment-sdk-setup.md)
  + [Rendszerkép-besorolási modell betanítása Azure Machine Learning](tutorial-train-models-with-aml.md)


- Ismerje meg a [gépi tanulási folyamatokat](/azure/machine-learning/service/concept-ml-pipelines) a gépi tanulási forgatókönyvek létrehozásához, optimalizálásához és felügyeletéhez.

- Olvassa el a részletes [Azure Machine learning architektúrát és fogalmakat](concept-azure-machine-learning-architecture.md) ismertető cikket.

- További információ: [a Microsoft egyéb gépi tanulási termékei](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).

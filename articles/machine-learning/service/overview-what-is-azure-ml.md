---
title: Mi a
titleSuffix: Azure Machine Learning service
description: A Azure Machine Learning szolgáltatás áttekintése – integrált, teljes körű adatelemzési megoldás professzionális adatszakértők számára a fejlett analitikai alkalmazások fejlesztéséhez, kipróbálásához és üzembe helyezéséhez Felhőbeli méretezéssel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: c1ec9db2b026dd1853eceb1f110878d6b2beb4d4
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678018"
---
# <a name="what-is-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatás?

A Azure Machine Learning Service egy felhőalapú szolgáltatás, amely a gépi tanulási modellek betanításához, üzembe helyezéséhez, automatizálásához és felügyeletéhez használható, mindezt a felhő által biztosított széles skálán.

## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás funkció?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulás használatával a számítógépek külön programozás nélkül tanulnak.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárol, a gépi tanulás segítséget nyújt más, a vásárolt termékek alapján. Vagy például a bankkártya lehúzásakor a gépi tanulás összeveti az adott tranzakciót az adatbázisában található tranzakciókkal, így segít a csalások felismerésében. Ha robotporszívóra bízza a szoba kitakarítását, a gépi tanulás segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="what-is-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatás?

A Azure Machine Learning szolgáltatás olyan felhőalapú környezetet biztosít, amellyel előkészítheti az adatfeldolgozást, betaníthatja, üzembe helyezheti, kezelheti és nyomon követheti a gépi tanulási modelleket. Indítsa el a képzést a helyi gépen, majd bővítse a felhőt. A szolgáltatás teljes mértékben támogatja a nyílt forráskódú technológiákat, például a PyTorch, a TensorFlow és a scikit, és bármilyen gépi tanuláshoz használható, klasszikus ml-ből mély tanulásra, felügyelt és nem felügyelt tanulásra. 

Felderítheti és előkészítheti az adatfeldolgozási és-tesztelési modelleket, és számos olyan eszközt alkalmazhat, mint például a:
+ Olyan [vizualizációs felület](ui-tutorial-automobile-price-train-score.md) , amelyben az n-drop modulok használatával felépítheti a kísérleteket, majd üzembe helyezheti a modelleket
+ [Jupyter jegyzetfüzetek](https://jupyter.org) , amelyekben az [SDK](https://docs.microsoft.com/azure/machine-learning/service/#reference) -k segítségével saját kódokat írhat, például a [minta jegyzetfüzeteket](https://aka.ms/aml-notebooks)
+ [Visual Studio Code-bővítmény](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Mire használhatom az Azure Machine Learning szolgáltatást?

A <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine learning PYTHON SDK</a> -t nyílt forráskódú Python-csomagokkal használhatja, vagy a [Visual Interface (előzetes verzió)](ui-tutorial-automobile-price-train-score.md) használatával magas szintű gépi tanulást és mélyreható tanulási modelleket építhet ki és taníthat ki egy Azure Machine learning-szolgáltatásban Munkaterület.

A nyílt forráskódú Python-csomagokban elérhető számos gépi tanulási összetevő közül választhat, például a <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-Learn</a>, a <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, a <a href="https://pytorch.org" target="_blank">PyTorch</a>és a <a href="https://mxnet.io" target="_blank">MXNet</a>.

Akár kódot ír, akár a vizualizáció felületét használja, több futtatást is nyomon követheti a legjobb megoldás megtalálásához, valamint az üzembe helyezett modellek kezeléséhez.

### <a name="code-first-experience"></a>Kód – első élmény

Indítsa el a képzést a helyi gépen a <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine learning PYTHON SDK</a> használatával, majd a felhőbe. Számos elérhető [számítási cél](how-to-set-up-training-targets.md), például a Azure Machine learning számítási és [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), valamint a [fejlett hiperparaméter-hangolási szolgáltatások](how-to-tune-hyperparameters.md)révén a felhő hatékonyságával gyorsabban hozhat létre jobb modelleket.

Az SDK segítségével [automatizálhatja a modell betanítását és finomhangolását](tutorial-auto-train-models.md) is.

### <a name="code-free--low-code-experience"></a>Kód – ingyenes/alacsony kódú élmény

A kód nélküli képzéshez próbálkozzon az alábbiakkal:

+ A drag-n-drop-kísérletezés és-telepítés vizuális felülete
    
    ![Azure Machine Learning szolgáltatás vizuális felülete](media/overview-what-is-azure-ml/visual-interface.png)

+ Az automatikus ML-kísérletek Azure Portali beállítása

### <a name="operationalization-mlops"></a>Operacionalizálási (MLOps)

Ha rendelkezik a megfelelő modellel, egyszerűen használhatja egy webszolgáltatásban, egy IoT-eszközön vagy Power BI. További információ: a [telepítésének és helyének](how-to-deploy-and-where.md)ismertetése. 

Ezután felügyelheti a telepített modelleket a Pythonhoz készült [Azure Machine learning SDK](https://aka.ms/aml-sdk) vagy a [Azure Portal](https://portal.azure.com/)használatával. 

Ezeket a modelleket felhasználhatja, és [valós időben](how-to-consume-web-service.md) vagy [aszinkron módon](how-to-run-batch-predictions.md) , nagy mennyiségű adattal lehet visszaadni az előrejelzéseket.

A fejlett [gépi tanulási folyamatokkal](concept-ml-pipelines.md)pedig az üzembe helyezés során az adatok előkészítése, a modell betanítása és a kiértékelés egyes lépésein is dolgozhat.

A Azure Machine Learning szolgáltatás használatának megkezdéséhez tekintse meg a [következő lépéseket](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Miben különbözik Azure Machine Learning szolgáltatás a Studiótól?

A [Machine learning Studio](../studio/what-is-ml-studio.md) egy együttműködésen alapuló, húzással ellátható vizualizációs munkaterület, amely képes gépi tanulási megoldások létrehozására, tesztelésére és üzembe helyezésére anélkül, hogy kódot kellene írnia. Előre összeépített és előre konfigurált gépi tanulási algoritmusokat és adatkezelési modulokat, valamint egy szabadalmaztatott számítási platformot használ.

A Azure Machine Learning szolgáltatás az SDK **-k és a-** a vizualizációs felületet (előzetes verzió) is lehetővé teszi az adatelőkészítés gyors előállításához, a gépi tanulási modellek betanításához és telepítéséhez. Ez a vizuális felület (előzetes verzió) hasonló húzási felületet biztosít a studióhoz. A Studio tulajdonosi számítási platformjának eltérően azonban a vizualizációs felület saját számítási erőforrásokat használ, és teljes mértékben integrálva van Azure Machine Learning szolgáltatásba.

Íme egy gyors összehasonlítás.

|| Machine Learning Studio | Azure Machine Learning szolgáltatás:<br/>Vizuális felület|
|---| --- | --- |
|| Általánosan elérhető (GA) | Előzetes verzióban|
|Illesztőfelületi modulok| Számos | Népszerű modulok kezdeti készlete|
|Számítási célok betanítása| Tulajdonosi számítási cél, csak CPU-támogatás| A Azure Machine Learning számítást, GPU-t vagy CPU-t támogatja.<br/>(Egyéb, az SDK-ban támogatott számítások)|
|Üzembe helyezési számítási célok| Tulajdonosi webszolgáltatás formátuma, nem testreszabható | Vállalati biztonsági beállítások & Azure Kubernetes szolgáltatás. <br/>([Egyéb](how-to-deploy-and-where.md) , az SDK-ban támogatott számítások) |
|Automatizált modellek betanítása és hiperparaméter finomhangolása | Nem | Még nem a Visual Interface-ben. <br/> (Támogatott az SDK-ban és a Azure Portal.) | 

Próbálja ki a vizualizációs felületet (előzetes verzió [) az Oktatóanyaggal: Az autó árának előrejelzése a vizualizációs](ui-tutorial-automobile-price-train-score.md)felületen.

> [!NOTE]
> A Studióban létrehozott modellek nem helyezhetők üzembe vagy nem kezelhetők Azure Machine Learning szolgáltatással. A szolgáltatás vizualizációs felületén létrehozott és üzembe helyezett modellek azonban a Azure Machine Learning szolgáltatás munkaterületén kezelhetők.

## <a name="free-trial"></a>Ingyenes próbaidőszak

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

Azure-szolgáltatásokra elkölthető krediteket kap. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez. Vagy [aktiválhatja az MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)előfizetői előnyeit, amelyek havi kreditet biztosítanak a fizetős Azure-szolgáltatásokhoz.

## <a name="next-steps"></a>További lépések

- A kezdéshez [hozzon létre egy Machine learning szolgáltatás](setup-create-workspace.md) -munkaterületet.

- Kövesse a teljes hosszúságú oktatóanyagokat: 
  + [Rendszerkép-besorolási modell betanítása Azure Machine Learning szolgáltatással](tutorial-train-models-with-aml.md) 
  + [Az adatelőkészítés és az automatizált gépi tanulás használata a regressziós modell automatikus betanításához](tutorial-data-prep.md)

- Ismerje meg a [gépi tanulási folyamatokat](/azure/machine-learning/service/concept-ml-pipelines) a gépi tanulási forgatókönyvek létrehozásához, optimalizálásához és felügyeletéhez.

- Olvassa el a részletes [Azure Machine Learning szolgáltatás architektúrája és fogalmak](concept-azure-machine-learning-architecture.md) cikk.

- További információ: [a Microsoft egyéb gépi tanulási termékei](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).

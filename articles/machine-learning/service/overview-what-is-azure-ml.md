---
title: Mi az
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning szolgáltatáshoz – egy integrált, teljes körű adatelemzési megoldás adatszakértők fejlesztése, kísérletezhet, és üzembe helyezéséhez a fejlett elemzési alkalmazások felhőhöz méretezett számára áttekintése.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 201ee251b195845e33ed3829be8540664811f2ab
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025287"
---
# <a name="what-is-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatás?

Az Azure Machine Learning szolgáltatás egy felhőalapú szolgáltatás, amellyel betanításához, üzembe helyezése, automatizálhatja és felügyelheti a machine learning-modellek, minden a felhőbeli biztosít széles körű skála.

## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás funkció?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. Machine learning segítségével számítógépek információ nélkül tanulhatnak.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Például ha online vásárlás, machine learning segít érdemes, hogy mit vásárolt alapján olyan termékeket. Vagy például a bankkártya lehúzásakor a gépi tanulás összeveti az adott tranzakciót az adatbázisában található tranzakciókkal, így segít a csalások felismerésében. Ha robotporszívóra bízza a szoba kitakarítását, a gépi tanulás segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="what-is-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatás?

Az Azure Machine Learning szolgáltatás segítségével előkészítheti az adatokat, betanítását, teszteléséhez, üzembe helyezése, kezelése és nyomon követheti a machine learning-modellek felhőalapú környezetet biztosít. Indítsa el a betanítási a helyi gépen, és a felhőben, majd horizontálisan felskálázható. A szolgáltatás teljes körűen támogatja a nyílt forráskódú technológiák, például a PyTorch, a tensorflow-hoz és a scikit-ismerje meg, és a machine learning klasszikus ml deep Learning bármilyen használható, a felügyelt és a tanítást. 

Ismerje meg, és előkészíti az adatokat, betanítását és tesztelni tudtuk és telepítheti őket számos eszközt használjon, például:
+ A [vizuális felhasználói felületet](ui-quickstart-run-experiment.md) amely is húzással n-modulok a kísérletek létrehozását és üzembe helyezhesse a modellek
+ [Jupyter-notebookok](https://jupyter.org) található, amely használja a [SDK-k](https://docs.microsoft.com/azure/machine-learning/service/#reference) megírhatja a kódot, mint például a [ezek mintafüzetek](https://aka.ms/aml-notebooks)
+ [Visual Studio Code extension](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Mire használhatom az Azure Machine Learning szolgáltatást?

Használatával a <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Python SDK</a> nyílt forráskódú Python-csomagokat, vagy használja a [vizuális felhasználói felületet (előzetes verzió)](ui-quickstart-run-experiment.md), létrehozhat és rendkívül pontos gépi tanulási és deep-learning modellek Azure Machine Learning-szolgáltatások munkaterület saját magának.

Választhat számos machine learning összetevő elérhető a nyílt forráskódú Python-csomagokat, mint például <a href="https://scikit-learn.org/stable/" target="_blank">Scikit további</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>, és <a href="https://mxnet.io" target="_blank">MXNet</a>.

Kód írása vagy a vizuális felhasználói felületet használja, hogy több futtatás, a legjobb megoldás, valamint a kezelheti az üzembe helyezett modellnél kísérletezést követheti nyomon.

### <a name="code-first-experience"></a>Kódközpontú élmény

Indítsa el a betanítási helyi használatával a <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Python SDK</a> , majd horizontálisan felskálázható a felhőbe. A legtöbb elérhető [számítási céljainak](how-to-set-up-training-targets.md), például az Azure Machine Learning Compute és [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), és a [hiperparaméter finomhangolása services speciális](how-to-tune-hyperparameters.md), hozhat létre gyorsabb segítségével kihasználhatja a felhőben hatékonyabb modelleket.

Emellett [automatizálhatja a modellek tanítása és finomhangolás](tutorial-auto-train-models.md) az SDK-val.

### <a name="code-free--low-code-experience"></a>Kódmentes / alacsony kódolási felületet

A kódmentes képzés, próbálja ki:

+ A vizuális felhasználói felületet, húzással n kísérletezés és üzembe helyezéséhez
    
    ![Az Azure Machine Learning szolgáltatás vizuális felhasználói felületet](media/overview-what-is-azure-ml/visual-interface.png)

+ Az Azure portál lehetőség automatizált Machine Learning-kísérletek

### <a name="operationalization-mlops"></a>Az operacionalizálás (MLOps)

Ha rendelkezik a megfelelő modellt, könnyen használható, egy webszolgáltatás, IoT-eszközökön, vagy a Power bi-BÓL. További információkért lásd a cikk [üzembe helyezése és hol](how-to-deploy-and-where.md). 

Az üzembe helyezett modellnél használata kezelheti, majd a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) vagy a [az Azure portal](https://portal.azure.com/). 

Ezek a modellek tudják használni, és az előrejelzések vissza [valós idejű](how-to-consume-web-service.md) vagy [aszinkron módon](how-to-run-batch-predictions.md) a nagy mennyiségű adatot.

És a speciális [gépi tanulási folyamatok](concept-ml-pipelines.md), az egyes lépések a adat-előkészítés, modell betanítása és kiértékelése fejlesztésig együttműködhet.

Ismerkedés az Azure Machine Learning szolgáltatás használatával, lásd: [további lépések](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Miben studióból az Azure Machine Learning szolgáltatás?

[A Machine Learning Studióban](../studio/what-is-ml-studio.md) egy együttműködést támogató, fogd és vidd vizuális munkaterület ahol készítése, tesztelése és üzembe helyezése a gépi tanulási megoldások létrehozása kód írása nélkül. Előre konfigurált, és előre összeállított gépi tanulási algoritmusokat használ, és adatkezelési modulokat, valamint a jogvédett számítási platform.

Az Azure Machine Learning szolgáltatás mindkét SDK-kat biztosít **- és -** egy vizuális interface(preview), hogy gyorsan előkészítheti az adatokat, betanítását és gépi tanulási modellek üzembe helyezése. A vizuális felhasználói felületet (előzetes verzió) Studio hasonló fogd és vidd élményt nyújt. Azonban az ellentétben a szellemi tulajdont képező számítási platform Studio, a vizuális felhasználói felületet a saját számítási erőforrást használ, és teljesen integrálva van az Azure Machine Learning szolgáltatás.

Íme egy gyors összehasonlítása.

|| Machine Learning Studio | Azure Machine Learning service:<br/>Vizuális felhasználói felületet|
|---| --- | --- |
|| Általánosan elérhető (GA) | Előzetes verzióban|
|Modulok interfész| Számos | A kezdeti beállítás népszerű-modulok|
|Képzési számítási célnak| Saját fejlesztésű számítási célnak, csak CPU-támogatás| Az Azure Machine Learning compute gpu-n és CPU támogatja.<br/>(Más számítási erőforrások támogatott SDK-ban)|
|Üzembe helyezés számítási célnak| Saját fejlesztésű webes szolgáltatás formátumú, nem testre szabható | Vállalati biztonsági beállítások és az Azure Kubernetes Service-ben. <br/>([Más számítási erőforrások](how-to-deploy-and-where.md) támogatott SDK-ban) |
|Automatizált modellek tanítása és a hiperparaméter finomhangolása | Nem | Még nincs a vizuális felhasználói felületet. <br/> (Az SDK-t és az Azure Portalon támogatott.) | 

Próbálja ki a vizuális felhasználói felületet (előzetes verzió) az [a rövid útmutató: Készítse elő és kód írása nélkül az adatok megjelenítése](ui-quickstart-run-experiment.md)

> [!NOTE]
> A Studióban létrehozott modelleket használva nem kell telepíteni, vagy az Azure Machine Learning szolgáltatás kezeli. Modellek létrehozása és telepítése a szolgáltatás vizuális felületen felügyelhető, az Azure Machine Learning szolgáltatás munkaterületén.

## <a name="free-trial"></a>Ingyenes próbaidőszak

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

Azure-szolgáltatásokra elkölthető krediteket kap. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez. Vagy [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), amely biztosítanak kreditek minden hónapban, amely használható a fizetős Azure-szolgáltatások.

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy Machine Learning szolgáltatás munkaterületet](setup-create-workspace.md) a kezdéshez.

- Kövesse a részletes oktatóanyagok: 
  + [Betanításához egy kép osztályozási modell Azure Machine Learning szolgáltatással](tutorial-train-models-with-aml.md) 
  + [Adatok előkészítése, és automatizált Machine learning automatikus – train egy regressziós modell segítségével](tutorial-data-prep.md)

- Használja a [az Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) az adatok előkészítéséhez.

- Ismerje meg a [gépi tanulási folyamatokat](/azure/machine-learning/service/concept-ml-pipelines) a gépi tanulási forgatókönyvek létrehozásához, optimalizálásához és felügyeletéhez.

- Olvassa el a részletes [Azure Machine Learning szolgáltatás architektúrája és fogalmak](concept-azure-machine-learning-architecture.md) cikk.

- További információkért lásd: [más gépi tanulási termékeit a Microsoft](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).

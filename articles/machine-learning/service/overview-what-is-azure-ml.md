---
title: Névjegy
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning szolgáltatáshoz – egy integrált, teljes körű adatelemzési megoldás adatszakértők fejlesztése, kísérletezhet, és üzembe helyezéséhez a fejlett elemzési alkalmazások felhőhöz méretezett számára áttekintése.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: f0860982da6a06a3529539768179f9bc28a0695a
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272983"
---
# <a name="what-is-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatás?

Az Azure Machine Learning szolgáltatás egy felhőszolgáltatás, amely segítségével betanításához, üzembe helyezése, automatizálhatja és kezelése a machine learning-modellek, minden a felhőbeli biztosít széles körű skála.

## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás funkció?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulással a számítógépek konkrét programozás nélkül tanulhatnak.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárol, a gépi tanulás képes a korábban megvásárolt cikkek alapján olyan termékeket ajánlani, amelyek esetleg szintén érdekelhetik Önt. Vagy például a bankkártya lehúzásakor a gépi tanulás összeveti az adott tranzakciót az adatbázisában található tranzakciókkal, így segít a csalások felismerésében. Ha robotporszívóra bízza a szoba kitakarítását, a gépi tanulás segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="what-is-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatás?

Az Azure Machine Learning szolgáltatás egy felhőalapú környezet gépi tanulási modellek fejlesztésére, betanítására, üzembe helyezésére, kezelésére és nyomon követésére.

[ ![Az Azure Machine Learning szolgáltatás munkafolyamata](./media/overview-what-is-azure-ml/aml.png) ] (. / media/overview-what-is-azure-ml/aml.png#lightbox)

Az Azure Machine Learning szolgáltatás teljes körűen támogatja a nyílt forráskódú technológiákat, így több tízezer nyílt forráskódú Python-csomaggal, többek között a TensorFlow-val és a scikit-learnnel is használható.
Számos eszközt, például támogatása [Jupyter notebookok](http://jupyter.org) vagy a [a Visual Studio Code az Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview) bővítmény, megkönnyíti az interaktív adatfeltárás, átalakíthatja, és majd fejlesztése és tesztelése modellek.
Az Azure Machine Learning szolgáltatás a [modellek automatizált generálására és finomhangolására szolgáló funkciókat](tutorial-auto-train-models.md) is tartalmaz, így segít könnyeddé, hatékonnyá és pontossá tenni az új modellek létrehozását.

Az Azure Machine Learning szolgáltatás lehetővé teszi, hogy a betanítást a helyi gépen kezdje, majd horizontálisan felskálázza a felhőbe. A legtöbb elérhető [számítási céljainak](how-to-set-up-training-targets.md) például az Azure Machine Learning Compute és [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), és a [hiperparaméter finomhangolása services speciális](how-to-tune-hyperparameters.md), hozhat létre jobb gyorsabb és modellek segítségével kihasználhatja a felhőben.

Ha létrehozta a megfelelő modellt, azt könnyedén üzembe is helyezheti egy tároló segítségével, például a Docker használatával. Ez azt jelenti, hogy egyszerű üzembe helyezése az Azure Container Instances szolgáltatásban vagy az Azure Kubernetes Service-ben, vagy a tárolót használja a saját telepítések, a helyszínen vagy a felhőben. További információkért lásd: a [üzembe helyezése és hol](how-to-deploy-and-where.md) dokumentumot.
A már üzembe helyezett modelleket kezelheti és futásukat nyomon követheti, így nyugodtan kísérletezhet addig, amíg megtalálhatja a megfelelő megoldást.
Üzembe helyezését követően a modell lépjen vissza az előrejelzések [valós idejű](how-to-consume-web-service.md), vagy [aszinkron módon](how-to-run-batch-predictions.md) a nagy mennyiségű adatot.

És a speciális [gépi tanulási folyamatok](concept-ml-pipelines.md), az adat-előkészítési, modell betanítása és kiértékelése és a központi telepítés összes lépését dolgozhatnak.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Mire használhatom az Azure Machine Learning szolgáltatást?

Az Azure Machine Learning szolgáltatás is automatikus-train-model és hangolni, az Ön számára.
Egy vonatkozó példáért lásd: [oktatóanyag: Automatikusan a az Azure automatikus Machine Learning egy osztályozási modell betanításához](tutorial-auto-train-models.md).

Az Azure Machine Learning használatával <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> Python, a nyílt forráskódú Python-csomagokat, valamint létrehozása és betanítása rendkívül pontos gépi tanulási és deep learning a modellek saját maga az Azure Machine Learning szolgáltatás munkaterületen.
Számos nyílt forráskódú Python-csomagban elérhető gépi tanulási-összetevő közül választhat, például:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Ha már rendelkezik egy modellt, használatával, hozzon létre egy tárolót (például a Dockert), amely tesztelési helyileg telepíthető. Ha végzett tesztelést, a modell vagy az Azure Container Instances, vagy az Azure Kubernetes Service-ben az éles webszolgáltatásként is telepíthető. További információkért lásd: a [üzembe helyezése és hol](how-to-deploy-and-where.md) dokumentumot.

Az üzembe helyezett modellnél használatával majd kezelheti a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) vagy a [az Azure portal](https://portal.azure.com/).
A modell kísérletek nyomon követése mellett kiértékelheti modell metrikákat, teljesített kapcsolat-újraépítési és ismételt üzembe helyezése a modell új verzióit.

Az Azure Machine Learning szolgáltatás használatához alább, a [További lépések](#next-steps) résznél kezdhet hozzá.

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Miben különbözik az Azure Machine Learning szolgáltatás a Studio használatától?

Az Azure Machine Learning Studio egy együttműködést ösztönző, az egér húzásával könnyedén vezérelhető vizuális munkaterület, amelyben anélkül készíthet, tesztelhet és helyezhet üzembe gépi tanulási modelleket, hogy kódot kellene írnia hozzá. Előre elkészített és előre konfigurált gépi tanulási algoritmusokat és adatkezelési modulokat használ.

Használja a Machine Learning Studiot akkor, amikor gyorsan és egyszerűen szeretne gépi tanulási modellekkel kísérletezni, és ha a megoldáshoz elegendőek a beépített gépi tanulási algoritmusok is.

A Machine Learning szolgáltatást pedig akkor, amikor Python környezetben dolgozik a gépi tanulási algoritmusok finomhangolásán, vagy ha nyílt forráskódú gépi tanulási kódtárakat venne igénybe.

> [!NOTE]
> Az Azure Machine Learning Studioban létrehozott modellek nem helyezhetők üzembe és nem kezelhetők az Azure Machine Learning szolgáltatással.

## <a name="free-trial"></a>Ingyenes próbaidőszak

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

Azure-szolgáltatásokra elkölthető krediteket kap. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez. Lehetőségként [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), amely kreditek minden hónapban biztosít Önnek, akkor használhat a fizetős Azure-szolgáltatások.

## <a name="next-steps"></a>További lépések

- Hozzon létre egy Machine Learning szolgáltatás-munkaterület a kezdéshez [Azure portal használatával](quickstart-get-started.md) vagy [pythonban](quickstart-create-workspace-with-python.md).

- A részletes oktatóanyag [Train és a egy kép besorolási modell az Azure Machine Learning üzembe helyezése](tutorial-train-models-with-aml.md).

- [Automatikus létrehozása az Azure Machine Learning és a egy modell autotune](tutorial-auto-train-models.md).

- Ismerje meg a [gépi tanulási folyamatokat](/azure/machine-learning/service/concept-ml-pipelines) a gépi tanulási forgatókönyvek létrehozásához, optimalizálásához és felügyeletéhez.

- Olvassa el a részletes [Azure Machine Learning szolgáltatás architektúrája és fogalmak](concept-azure-machine-learning-architecture.md) cikk.

- Más gépi tanulási termékeit a Microsoft további információkért lásd: [más gépi tanulási termékeit a Microsoft](./overview-more-machine-learning.md).


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->

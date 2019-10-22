---
title: Vizuális felület
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg az Azure Machine Learning vizuális felületét (előzetes verzió) alkotó feltételekkel, fogalmakkal és munkafolyamatokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 9/23/2019
ms.openlocfilehash: a23f123c6ffadaad4f830e1f4eab3820e6ef56f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692227"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Mi a Azure Machine Learning vizuális felülete? 

A Azure Machine Learning vizuális felülete (előzetes verzió) lehetővé teszi a gépi tanulási modellek előkészítését, használatát, tesztelését, üzembe helyezését, kezelését és nyomon követését programkód írása nélkül.

Nincs szükség programozásra, vizuálisan összekapcsolhatók az [adatkészletek](#datasets) és [modulok](#module) a modell létrehozásához.

A vizuális felület a Azure Machine Learning [munkaterületét](concept-workspace.md) használja a következőhöz:

+ [Folyamatok](#pipeline) létrehozása, szerkesztése és futtatása a munkaterületen.
+ Hozzáférési [adatkészletek](#datasets).
+ A folyamat futtatásához használja a munkaterület [számítási erőforrásait](#compute) . 
+ [Modellek](concept-azure-machine-learning-architecture.md#models)regisztrálása.
+ Folyamatok [közzététele](#publish) Rest-végpontként.
+ A modelleket [üzembe helyezheti](#deployment) folyamat-végpontként (a Batch-következtetésekhez) vagy a valós idejű végpontokat a munkaterületen lévő számítási erőforrásokon.

![A vizuális felület áttekintése](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Munkafolyamat

A vizuális felület interaktív, vizualizációs vászon használatával gyorsan kiépítheti, tesztelheti és megismételheti a modelleket. 

+ A vászonra húzhatja az [adatkészleteket](#datasets) és a [modulokat](#module) .
+ Összekapcsolhatja a modulokat egy [folyamat](#pipeline)létrehozásához.
+ Futtassa a folyamatot a Machine Learning szolgáltatás munkaterület számítási erőforrásának használatával.
+ Ismételje meg a modell kialakítását a folyamat szerkesztésével és az újbóli futtatásával.
+ Ha elkészült, alakítsa át a **betanítási** folyamatot egy **következtetési folyamatra**.
+ [Tegye közzé](#publish) a folyamatot Rest-végpontként, ha újra el szeretné küldeni a Python-kód nélkül.
+ A következtetési folyamat [üzembe helyezése](#deployment) folyamat-végpontként vagy valós idejű végpontként, hogy a modell mások számára is elérhető legyen.

## <a name="pipeline"></a>Folyamat

Hozzon létre egy teljesen [új ml-](concept-azure-machine-learning-architecture.md#ml-pipelines) folyamatot, vagy használjon sablonként egy meglévő mintát. Minden alkalommal, amikor futtat egy folyamatot, az összetevők a munkaterületen tárolódnak. A folyamat-futtatások [kísérletekbe](concept-azure-machine-learning-architecture.md#experiments)vannak csoportosítva.

A folyamatok adatkészleteket és analitikai modulokból állnak, amelyek egy modell összeállításához kapcsolódnak. Pontosabban, egy érvényes folyamat a következő jellemzőkkel rendelkezik:

* Az adatkészletek csak modulokhoz csatlakoztathatók.
* A modulok lehetnek adatkészletekhez vagy más modulokhoz csatlakoztathatók.
* A modulok összes bemeneti portjának csatlakoznia kell az adatfolyamhoz.
* Az egyes modulokhoz szükséges összes paramétert be kell állítani.


A vizuális felület megismeréséhez lásd [: oktatóanyag: az autó árának előrejelzése a vizualizációs felületen](ui-tutorial-automobile-price-train-score.md).

## <a name="datasets"></a>Adathalmazok

A Machine learning-adatkészlet megkönnyíti az adataihoz való hozzáférést és azokkal való munkavégzést. A vizuális kezelőfelület számos minta adatkészletet tartalmaz, amelyekkel kísérletezhet. A szükségesnél több adatkészletet is [regisztrálhat](./how-to-create-register-datasets.md) .

## <a name="module"></a>Modul

A modulok az adatokon végezhető algoritmusok. A vizualizációs felület számos modult tartalmaz, amelyek az adatok beáramlása függvénytől kezdve a képzésre, a pontozásra és az érvényesítési folyamatokra terjednek ki.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paramétereinek a vászontól jobbra látható Tulajdonságok ablaktáblán jelennek meg. A modell hangolásához módosíthatja a paramétereket a panelen.

![Modul tulajdonságai](media/ui-concept-visual-interface/properties.png)

Ha segítségre van az elérhető gépi tanulási algoritmusok könyvtára között, tekintse meg a következő témakört: [algoritmus & modul ismertetése – áttekintés](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Számítási erőforrások

A munkaterületen lévő számítási erőforrások használatával futtathatja a folyamatot, és üzemeltetheti a telepített modelleket valós idejű végpontok vagy folyamat-végpontok (a Batch-következtetések esetében). A támogatott számítási célok a következők:

| Számítási cél | Képzés | Üzembe helyezés |
| ---- |:----:|:----:|
| Azure Machine Learning számítás | ✓ | |
| Azure Kubernetes Service | | ✓ |

A számítási célok a Machine Learning [munkaterülethez](concept-workspace.md)vannak csatolva. A számítási célokat a munkaterületen a [Azure Portal](https://portal.azure.com) vagy a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)kezelheti.

## <a name="publish"></a>Közzététel

Ha elkészült egy folyamattal, közzéteheti REST-végpontként. A [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) a létrehozott Python-kód nélkül is elküldhető.

Emellett a PublishedPipeline a különböző PipelineParameter értékekkel és bemenetekkel rendelkező folyamatok újraküldésére is használható.

## <a name="deployment"></a>Üzembe helyezés

Ha a prediktív modell elkészült, telepítse azt egy folyamat-végpontként vagy a valós idejű végpontként közvetlenül a vizualizáció felületéről.

A folyamat végpontja egy [PublishedPipeline, amely elküldheti a folyamat futását különböző PipelineParameter értékekkel és bemenetekkel a Batch-következtetésekhez.

A valós idejű végpont az alkalmazás és a pontozási modell közötti felületet biztosít. Egy külső alkalmazás valós időben tud kommunikálni a pontozási modellel. A valós idejű végpontok hívása egy külső alkalmazásnak adja vissza az előrejelzési eredményeket. Egy valós idejű végpont hívásához át kell adnia egy API-kulcsot, amelyet a rendszer a végpont üzembe helyezésekor hozott létre. A végpont egy REST-alapú, népszerű architektúra a webes programozási projektekhez.

A modell üzembe helyezésével kapcsolatos további információkért lásd [: oktatóanyag: Machine learning-modell üzembe helyezése a vizualizációs felületen](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Következő lépések

* A prediktív elemzés és a gépi tanulás alapjai a [bemutatóban: az autó árának előrejelzése a vizualizációs felületen](ui-tutorial-automobile-price-train-score.md)
* Használja az egyik mintát, és módosítsa az igényeinek megfelelően:

    * [1. példa – regresszió: előrejelzési ár](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [2. minta – regresszió: előrejelzési ár és összehasonlítási algoritmusok](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [3. példa – besorolás: hitelkockázat előrejelzése](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [5. példa – besorolás: forgalom előrejelzése, szolgáltatóváltást és értékesítés](how-to-ui-sample-classification-predict-churn.md)
    * [6. példa – besorolás: repülési késések előrejelzése](how-to-ui-sample-classification-predict-flight-delay.md)


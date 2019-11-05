---
title: ML modellek készítése tervezővel
titleSuffix: Azure Machine Learning
description: Ismerje meg a Azure Machine Learning tervezőjét alkotó feltételeket, fogalmakat és munkafolyamatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517864"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Mi az a Azure Machine Learning Designer (előzetes verzió)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

A Azure Machine Learning tervezője lehetővé teszi a gépi tanulási modellek előkészítését, használatát, tesztelését, üzembe helyezését, kezelését és nyomon követését kód írása nélkül.

Nincs szükség programozásra, vizuálisan összekapcsolhatók az [adatkészletek](#datasets) és [modulok](#module) a modell létrehozásához.

A tervező az Azure Machine Learning [munkaterületet](concept-workspace.md) használja a következőhöz:

+ [Folyamatok](#pipeline) létrehozása, szerkesztése és futtatása a munkaterületen.
+ Hozzáférési [adatkészletek](#datasets).
+ A folyamat futtatásához használja a munkaterület [számítási erőforrásait](#compute) . 
+ [Modellek](concept-azure-machine-learning-architecture.md#models)regisztrálása.
+ Folyamatok [közzététele](#publish) Rest-végpontként.
+ A modelleket [üzembe helyezheti](#deployment) folyamat-végpontként (a Batch-következtetésekhez) vagy a valós idejű végpontokat a munkaterületen lévő számítási erőforrásokon.

![A tervező áttekintése](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Munkafolyamat

A Designer interaktív, vizualizációs vásznat biztosít a modell gyors létrehozásához, teszteléséhez és megismétléséhez. 

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


A tervező használatba vételének megismeréséhez tekintse meg [a következő oktatóanyagot: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md).

## <a name="datasets"></a>Adathalmazok

A Machine learning-adatkészlet megkönnyíti az adataihoz való hozzáférést és azokkal való munkavégzést. A Designerben számos minta adatkészletet tartalmaz a kísérletezéshez. A szükségesnél több adatkészletet is [regisztrálhat](./how-to-create-register-datasets.md) .

## <a name="module"></a>Modul

A modulok az adatokon végezhető algoritmusok. A Designer számos modult tartalmaz, amelyek az adatok beáramlása funkciótól kezdve betanítási, pontozási és érvényesítési folyamatokat biztosítanak.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paramétereinek a vászontól jobbra látható Tulajdonságok ablaktáblán jelennek meg. A modell hangolásához módosíthatja a paramétereket a panelen.

![Modul tulajdonságai](media/ui-concept-visual-interface/properties.png)

Ha segítségre van az elérhető gépi tanulási algoritmusok könyvtára között, tekintse meg a következő témakört: [algoritmus & modul ismertetése – áttekintés](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Számítási erőforrások

A munkaterületen lévő számítási erőforrások használatával futtathatja a folyamatot, és üzemeltetheti a telepített modelleket valós idejű végpontok vagy folyamat-végpontok (a Batch-következtetések esetében). A támogatott számítási célok a következők:

| Számítási cél | Képzés | Környezet |
| ---- |:----:|:----:|
| Azure Machine Learning számítás | ✓ | |
| Azure Kubernetes Service | | ✓ |

A számítási célok a Machine Learning [munkaterülethez](concept-workspace.md)vannak csatolva. A számítási célokat a munkaterületen [Azure Machine learning Studióban](https://ml.azure.com)kezelheti.

## <a name="publish"></a>Közzététel

Ha elkészült egy folyamattal, közzéteheti REST-végpontként. A [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) a létrehozott Python-kód nélkül is elküldhető.

Emellett a PublishedPipeline a különböző PipelineParameter értékekkel és bemenetekkel rendelkező folyamatok újraküldésére is használható.

## <a name="deployment"></a>Környezet

Ha a prediktív modell elkészült, telepítse azt egy folyamat-végpontként vagy a valós idejű végpontot közvetlenül a tervezőtől.

A folyamat végpontja egy PublishedPipeline, amely elküldheti a folyamat futását különböző PipelineParameter értékekkel és bemenetekkel a Batch-következtetésekhez.

A valós idejű végpont az alkalmazás és a pontozási modell közötti felületet biztosít. Egy külső alkalmazás valós időben tud kommunikálni a pontozási modellel. A valós idejű végpontok hívása egy külső alkalmazásnak adja vissza az előrejelzési eredményeket. Egy valós idejű végpont hívásához át kell adnia egy API-kulcsot, amelyet a rendszer a végpont üzembe helyezésekor hozott létre. A végpont egy REST-alapú, népszerű architektúra a webes programozási projektekhez.

A modell üzembe helyezésével kapcsolatos további információkért lásd [: oktatóanyag: Machine learning-modell üzembe helyezése a Designer](tutorial-designer-automobile-price-deploy.md)használatával.

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Áthelyezés a vizualizáció kezelőfelületéről a tervezőbe

A Visual Interface (előzetes verzió) frissítve lett, és mostantól Azure Machine Learning Designer (előzetes verzió). A tervező olyan folyamat-alapú háttérrendszer használatára lett újratervezve, amely teljesen integrálódik a Azure Machine Learning többi szolgáltatásával. 

A frissítések eredményeképpen a vizuális felület egyes fogalmai és kifejezései módosultak vagy átnevezve lettek. A legfontosabb koncepcionális változásokért tekintse meg az alábbi táblázatot. 

| Koncepció a Designerben | Korábban a vizuális felületen |
| ---- |:----:|
| Folyamat piszkozata | Kísérlet |
| Valós idejű végpont | Webszolgáltatás |

### <a name="migrating-to-the-designer"></a>Migrálás a tervezőbe

A meglévő vizuális felületi kísérleteket és webszolgáltatásokat átalakíthatja folyamatokra és valós idejű végpontokra a tervezőben. A Visual Interface-eszközök áttelepíthetők a következő lépésekkel:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>További lépések

* A prediktív elemzés és a gépi tanulás alapjai a [bemutatóban: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)
* Használja az egyik mintát, és módosítsa az igényeinek megfelelően:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-designer-sample-regression-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [3. minta – besorolás a szolgáltatás kiválasztásával: bevétel előrejelzése](how-to-designer-sample-classification-predict-income.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-designer-sample-classification-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
- [7. minta – szöveges besorolás: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)


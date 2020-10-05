---
title: Mi az Azure Machine Learning Studio?
description: A Azure Machine Learning Studio egy Azure Machine Learning-munkaterületek webes portálja. A Studio a kód nélküli és a kód első lépéseit kombinálva létrehoz egy inkluzív adatelemzési platformot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 7b0db0f97531c60b3e809dd0dafa042dbcc2b542
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91667435"
---
# <a name="what-is-azure-machine-learning-studio"></a>Mi az Azure Machine Learning Studio?

Ebből a cikkből megtudhatja, hogyan Azure Machine Learning Studio, az adattudós-fejlesztőknek szóló webes portál [Azure Machine learning](overview-what-is-azure-ml.md). A Studio a kód nélküli és a kód első lépéseit kombinálja egy inkluzív adatelemzési platformon.

Ebből a cikkből megtudhatja:
>[!div class="checklist"]
> - [Gépi tanulási projektek](#author-machine-learning-projects) készítése a Studióban.
> - [Eszközök és erőforrások kezelése](#manage-assets-and-resources) a Studióban.
> - A [Azure Machine learning Studio és ml Studio (klasszikus)](#ml-studio-classic-vs-azure-machine-learning-studio)közötti különbségek.


## <a name="author-machine-learning-projects"></a>Gépi tanulási projektek készítése

A Studio a projekt típusától és a felhasználói élmény szintjétől függően több szerzői tapasztalatot kínál.

+ **Jegyzetfüzetek**

  Saját kódot írhat és futtathat felügyelt [Jupyter notebook-kiszolgálókon](how-to-run-jupyter-notebooks.md) , amelyek közvetlenül a studióba vannak integrálva. 

+ **Azure Machine Learning Designer**

  A Designer használatával a gépi tanulási modelleket bármilyen kód írása nélkül betaníthatja és üzembe helyezheti. Az adatkészletek és modulok húzásával ML-folyamatokat hozhat létre. Próbálja ki a [tervezői oktatóanyagot](tutorial-designer-automobile-price-train-score.md).

    ![Azure Machine Learning Designer – példa](media/concept-designer/designer-drag-and-drop.gif)

+ **Automatikus gépi tanulás felhasználói felülete**

  Megtudhatja, hogyan hozhat létre egy könnyen használható kezelőfelülettel rendelkező [AUTOMATIZÁLT ml-kísérleteket](tutorial-first-experiment-automated-ml.md) . 

  [![Azure Machine Learning Studio navigációs panelje](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Adatfelirat**

    A [Azure Machine learning adatcímkéző](how-to-create-labeling-projects.md) használatával hatékonyan koordinálhatja az adatcímkéző projekteket.

## <a name="manage-assets-and-resources"></a>Eszközök és erőforrások kezelése

A gépi tanulási eszközöket közvetlenül a böngészőben kezelheti. Az eszközök az SDK és a Studio között azonos munkaterületen vannak megosztva zökkenőmentes felhasználói élmény érdekében. A Studio használatával kezelheti a következőket:

- Modellek
- Adathalmazok
- Adattárak
- Számítási erőforrások
- Notebooks
- Kísérletek
- Naplók futtatása
- Pipelines 
- Folyamat végpontjai

Még ha tapasztalt fejlesztő is, a Studio leegyszerűsítheti a munkaterület-erőforrások kezelését.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (klasszikus) vs Azure Machine Learning Studio

Az 2015-as kiadásban megjelent **ml Studio (klasszikus)** volt az első drag-and-drop Machine learning-szerkesztő. Ez egy önálló szolgáltatás, amely csak vizuális élményt nyújt. A Studio (klasszikus) nem működik együtt Azure Machine Learningokkal.

A **Azure Machine learning** egy különálló és modernizált szolgáltatás, amely teljes adatelemzési platformot biztosít. Támogatja mind a Code-First, mind az alacsony kódú élményt.

A **Azure Machine learning Studio** *egy Azure Machine learning* webportálja, amely alacsony kódú és kód nélküli lehetőségeket tartalmaz a projektek készítéséhez és az eszközök kezeléséhez. 

Azt javasoljuk, hogy az új felhasználók ML Studio (klasszikus) helyett **Azure Machine learning**válasszanak az adatelemzési eszközök legújabb tartományához.

### <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Az alábbi táblázat összefoglalja ML Studio (klasszikus) és Azure Machine Learning közötti fő különbségeket.

| Szolgáltatás | ML Studio (klasszikus) | Azure Machine Learning |
|---| --- | --- |
| Húzási interfész | Klasszikus felhasználói élmény | Frissített tapasztalat – [Azure Machine learning Designer](concept-designer.md)| 
| SDK-kód | Nem támogatott | Teljes mértékben integrált [Azure Machine learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/) és [R](tutorial-1st-r-experiment.md) SDK-k |
| Experiment | Skálázható (10 GB-os betanítási adatkorlát) | Méretezés számítási céllal |
| Számítási célok betanítása | Tulajdonosi számítási cél, csak CPU-támogatás | Testreszabható [képzési számítási célok](concept-compute-target.md#train)széles köre. GPU-val és CPU-támogatással rendelkezik | 
| Üzembe helyezési számítási célok | Tulajdonosi webszolgáltatás formátuma, nem testreszabható | Testreszabható [üzembe helyezési számítási célok](concept-compute-target.md#deploy)széles köre. GPU-val és CPU-támogatással rendelkezik |
| ML-folyamat | Nem támogatott | Rugalmas, moduláris [folyamatok](concept-ml-pipelines.md) készítése a munkafolyamatok automatizálására |
| MLOps | Alapszintű modell kezelése és üzembe helyezése; Csak CPU-telepítések | Entitások verziószámozása (modell, adattárolás, munkafolyamatok), munkafolyamat-automatizálás, integráció a vel-eszközökkel, processzor-és GPU-környezetek, [valamint egyebek](concept-model-management-and-deployment.md) |
| Modell formátuma | Saját formátum, Studio (klasszikus) csak | Több támogatott formátum a betanítási feladattípustól függően |
| Automatizált modellek betanítása és hiperparaméter finomhangolása |  Nem támogatott | [Támogatott](concept-automated-ml.md). Kód – az első és a kód nélküli beállítások. | 
| Adateltolódás észlelése | Nem támogatott | [Támogatott](how-to-monitor-datasets.md) |
| Adatcímkéző projektek | Nem támogatott | [Támogatott](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>További lépések

Látogasson el a [studióba](https://ml.azure.com), vagy Ismerje meg a különböző szerzői lehetőségeket a következő oktatóanyagokkal:  
  + [Python-jegyzetfüzetek használata &-modellek üzembe helyezéséhez](tutorial-1st-experiment-sdk-setup.md)
  + [Az automatizált gépi tanulás használata a & üzembe helyezési modelljeinek betanításához](tutorial-first-experiment-automated-ml.md)  
  + [A Designer használata & üzembe helyezési modelljeinek betanításához](tutorial-designer-automobile-price-train-score.md)


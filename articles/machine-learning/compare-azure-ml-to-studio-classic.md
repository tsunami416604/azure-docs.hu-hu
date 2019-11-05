---
title: Azure Machine Learning vs. ML Studio (klasszikus)
description: Miben különbözik a Azure Machine Learning ML Studiotól (klasszikus)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: 44fe7d4e51e50cdb3d1f1ed598f760113fb623bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502078"
---
# <a name="how-azure-machine-learning-differs-from-ml-studio-classic"></a>Miben különbözik a Azure Machine Learning ML Studiotól (klasszikus)

Ez a cikk a Azure Machine Learning funkcióit, képességeit és felületét hasonlítja össze ML Studio (klasszikus). 

## <a name="about-ml-studio-classic"></a>Tudnivalók a ML Studioról (klasszikus)
A [ml Studio (klasszikus)](/studio/what-is-ml-studio.md) egy együttműködésen alapuló, húzással ellátható vizualizációs munkaterület, ahol a gépi tanulási megoldások létrehozásához, teszteléséhez és üzembe helyezéséhez nem szükséges programkódot írni. Előre összeépített és előre konfigurált gépi tanulási algoritmusokat és adatkezelési modulokat, valamint egy szabadalmaztatott számítási platformot használ.

## <a name="about-azure-machine-learning"></a>Tudnivalók az Azure Machine Learningről

Eközben [Azure Machine learning](/service/overview-what-is-azure-ml.md) a Designer (előzetes verzió) nevű webes felületet **és** számos SDK-t és CLI-t is biztosít az adatelőkészítéshez, a gépi tanulási modellek betanításához és üzembe helyezéséhez. A Azure Machine Learning a méretezés, a több keretrendszer támogatása, a speciális ML-képességek, például az automatizált gépi tanulás és a folyamat-támogatás.

A Azure Machine Learning Designer hasonló húzási élményt nyújt a studióhoz (klasszikus). A Studio (klasszikus) tulajdonosi számítási platformtól eltérően a tervező a saját számítási erőforrásait használja, méretezhető és teljes mértékben integrálva van Azure Machine Learningba.  

> [!TIP]
> A jelenleg Machine Learning Studiot (klasszikus) használó ügyfeleket javasoljuk a [Azure Machine learning Designer](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) (előzetes verzió) kipróbálására, amely a drag and drop ml modulokat, __valamint__ a skálázhatóságot, a verziókövetés és a vállalati biztonságot is biztosítja.

## <a name="comparison-azure-machine-learning-vs-ml-studio-classic"></a>Összehasonlítás: Azure Machine Learning vs. ML Studio (klasszikus)

Íme egy gyors összehasonlítás.

||  Azure Machine Learning Designer|Studio (klasszikus) |
|---| --- | --- |
||A Designer előzetes verzióban érhető el, Azure Machine Learning a GA|Általánosan elérhető (GA) | 
|Húzási interfész| Igen | Igen|
|Kísérlet| Méretezés számítási céllal|Scale (10GB betanítási adatkorlátja) | 
|Illesztőfelületi modulok|  Számos népszerű modul|Számos |
|Számítási célok betanítása| PÉNZMOSÁS-számítás (GPU/CPU)|Tulajdonosi számítási cél, csak CPU|
|Viszonyítási számítási célok| Azure Kubernetes szolgáltatás valós idejű következtetéshez <br/>PÉNZMOSÁS-számítás a Batch-következtetéshez|Tulajdonosi webszolgáltatás formátuma, nem testreszabható | 
|ML-folyamat| Folyamat létrehozása <br/> közzétett folyamat <br/> Folyamat végpontja <br/> [További információ az ML-folyamatról](service/concept-ml-pipelines.md)|Nem támogatott | 
|ML-Ops| Konfigurálható üzembe helyezés, modell és folyamat verziószámozása|Alapszintű modell kezelése és üzembe helyezése | 
|Modell| A standard formátum, amely a betanítási feladatoktól függ|Védett, nem hordozható formátum.| 
|Automatizált modellek betanítása|Még nem a tervezőben, hanem a felületen és az SDK-n keresztül lehetséges.| Nem | 

## <a name="get-started-with-azure-machine-learning-designer"></a>Ismerkedés a Azure Machine Learning Designer alkalmazással

A következő források segíthetnek a Azure Machine Learning megkezdésében

- A [Azure Machine learning áttekintése](service/tutorial-first-experiment-automated-ml.md) 

- [Hozza létre első tervezői folyamatát](service/tutorial-designer-automobile-price-train-score.md) az automatikus árak előrejelzéséhez.


![Azure Machine Learning Designer – példa](service/media/concept-ml-pipelines/visual-design-surface.gif)

## <a name="next-steps"></a>További lépések

A tervezőben a drag-n-drop képességeken kívül a Azure Machine Learning további elérhető eszközöket is tartalmaz:  
  + [Python-jegyzetfüzetek használata & ML-modellek üzembe helyezéséhez](./service/tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata & ML-modellek üzembe helyezéséhez](./service/tutorial-1st-r-experiment.md) 
  + [Az automatizált gépi tanulás használata & ML-modellek üzembe helyezéséhez](./service/tutorial-designer-automobile-price-train-score.md) 
  + [Modellek betanítása és üzembe helyezése a Machine learning parancssori felület használatával](./service/tutorial-train-deploy-model-cli.md)


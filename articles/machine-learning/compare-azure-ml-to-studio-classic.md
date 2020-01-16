---
title: Azure Machine Learning vs. Machine Learning Studio (klasszikus)
description: Miben különbözik a Azure Machine Learning Machine Learning Studiotól (klasszikus)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974137"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Miben különbözik a Azure Machine Learning Machine Learning Studiotól (klasszikus)

Ez a cikk a Azure Machine Learning funkcióit, képességeit és felületét hasonlítja össze Machine Learning Studio (klasszikus). 

## <a name="about-machine-learning-studio-classic"></a>Tudnivalók a Machine Learning Studioról (klasszikus)
A [Machine learning Studio (klasszikus)](studio/what-is-ml-studio.md) egy együttműködésen alapuló, húzással ellátható vizualizációs munkaterület, ahol a gépi tanulási megoldások létrehozásához, teszteléséhez és üzembe helyezéséhez nem szükséges programkódot írni. Előre összeépített és előre konfigurált gépi tanulási algoritmusokat és adatkezelési modulokat, valamint egy szabadalmaztatott számítási platformot használ.

## <a name="about-azure-machine-learning"></a>Tudnivalók az Azure Machine Learningről

Eközben [Azure Machine learning](overview-what-is-azure-ml.md) a Designer (előzetes verzió) nevű webes felületet **és** számos SDK-t és CLI-t is biztosít az adatelőkészítéshez, a gépi tanulási modellek betanításához és üzembe helyezéséhez. A Azure Machine Learning a méretezés, a több keretrendszer támogatása, a speciális ML-képességek, például az automatizált gépi tanulás és a folyamat-támogatás.

A Azure Machine Learning Designer hasonló húzási élményt nyújt a studióhoz (klasszikus). A Studio (klasszikus) tulajdonosi számítási platformtól eltérően a tervező a saját számítási erőforrásait használja, méretezhető és teljes mértékben integrálva van Azure Machine Learningba.  

> [!TIP]
> A jelenleg Machine Learning Studiot (klasszikus) használó ügyfeleket javasoljuk a [Azure Machine learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer) (előzetes verzió) kipróbálására, amely a drag and drop ml modulokat, __valamint__ a skálázhatóságot, a verziókövetés és a vállalati biztonságot is biztosítja.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Összehasonlítás: Azure Machine Learning vs. Machine Learning Studio (klasszikus)

Íme egy gyors összehasonlítás.

||  Azure Machine Learning Designer|Studio (klasszikus) |
|---| --- | --- |
||A tervező előzetes verzióban érhető el, Azure Machine Learning a GA|Általánosan elérhető (GA) | 
|Húzással használható felület| Igen | Igen|
|Kísérlet| Méretezés számítási céllal|Scale (10GB betanítási adatkorlátja) | 
|Illesztőfelületi modulok| [Számos népszerű modul](algorithm-module-reference/module-reference.md) | Számos |
|Számítási célok betanítása| PÉNZMOSÁS-számítás (GPU/CPU)|Tulajdonosi számítási cél, csak CPU|
|Viszonyítási számítási célok| Azure Kubernetes szolgáltatás valós idejű következtetéshez <br/>PÉNZMOSÁS-számítás a Batch-következtetéshez|Tulajdonosi webszolgáltatás formátuma, nem testreszabható | 
|ML-folyamat| Folyamat létrehozása <br/> Közzétett folyamat <br/> Folyamat végpontja <br/> [További információ az ML-folyamatról](concept-ml-pipelines.md)|Nem támogatott | 
|ML-Ops| Konfigurálható üzembe helyezés, modell és folyamat verziószámozása|Alapszintű modell kezelése és üzembe helyezése | 
|Modell| A standard formátum, amely a betanítási feladatoktól függ|Védett, nem hordozható formátum.| 
|Automatizált modellek betanítása|Még nem a tervezőben, hanem a felületen és az SDK-n keresztül lehetséges.| Nem | 

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés az Azure Machine Learning szolgáltatással

A következő források segíthetnek a Azure Machine Learning megkezdésében

- A [Azure Machine learning áttekintése](tutorial-first-experiment-automated-ml.md) 

- [Hozza létre első tervezői folyamatát](tutorial-designer-automobile-price-train-score.md) az automatikus árak előrejelzéséhez.

![Azure Machine Learning Designer – példa](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Következő lépések

A tervezőben a fogd és vidd funkció mellett a Azure Machine Learning más eszközök is elérhetők:  
  + [Python-jegyzetfüzetek használata & ML-modellek üzembe helyezéséhez](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata & ML-modellek üzembe helyezéséhez](tutorial-1st-r-experiment.md) 
  + [Az automatizált gépi tanulás használata & ML-modellek üzembe helyezéséhez](tutorial-designer-automobile-price-train-score.md) 
  + [Modellek betanítása és üzembe helyezése a Machine learning parancssori felület használatával](tutorial-train-deploy-model-cli.md)


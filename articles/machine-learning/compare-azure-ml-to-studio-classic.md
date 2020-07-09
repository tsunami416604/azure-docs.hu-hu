---
title: Azure Machine Learning vs. Machine Learning Studio (klasszikus)
description: Mi a különbség a Azure Machine Learning és a Machine Learning Studio (klasszikus) között?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 065fd166fb2ce82a3338322d55ef1ee5886ac56b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210371"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (klasszikus)

Ebben a cikkben megismerheti a Azure Machine Learning és Machine Learning Studio (klasszikus) közötti különbséget. 

A Azure Machine Learning a Python és az R SDK-kat **, valamint** a "drag-and-drop" designert biztosít a gépi tanulási modellek létrehozásához és üzembe helyezéséhez. A Studio (klasszikus) csak egy különálló fogd-és-drop-élményt biztosít.

Azt javasoljuk, hogy az új felhasználók a legszélesebb körben válasszanak Azure Machine Learning a legmodernebb gépi tanulási eszközökhöz.

## <a name="quick-comparison"></a>Gyors összehasonlítás

Az alábbi táblázat a Azure Machine Learning és a Studio (klasszikus) közötti főbb különbségeket foglalja össze:

| | Machine Learning Studio (klasszikus) | Azure Machine Learning |
|---| --- | --- |
| Húzási interfész | Támogatott | Támogatott – [Azure Machine learning Designer (előzetes verzió)](concept-designer.md) <br/>(Vállalati munkaterület szükséges hozzá) | 
| Experiment | Skálázható (10 GB-os betanítási adatkorlát) | Méretezés számítási céllal |
| Számítási célok betanítása | Tulajdonosi számítási cél, csak CPU-támogatás | Testreszabható [képzési számítási célok](concept-compute-target.md#train)széles köre. GPU-val és CPU-támogatással rendelkezik | 
| Üzembe helyezési számítási célok | Tulajdonosi webszolgáltatás formátuma, nem testreszabható | Testreszabható [üzembe helyezési számítási célok](concept-compute-target.md#deploy)széles köre. GPU-val és CPU-támogatással rendelkezik |
| ML-folyamat | Nem támogatott | Rugalmas, moduláris [folyamatok](concept-ml-pipelines.md) készítése a munkafolyamatok automatizálására |
| MLOps | Alapszintű modell kezelése és üzembe helyezése | Entitás verziószámozása (modell, adatfeldolgozás), munkafolyamat-automatizálás, integráció a vel-eszközökkel [és egyebek](concept-model-management-and-deployment.md) |
| Modell formátuma | Saját formátum, Studio (klasszikus) csak | Több támogatott formátum a betanítási feladattípustól függően |
| Automatizált modellek betanítása és hiperparaméter finomhangolása |  Nem támogatott | [Az SDK és a Visual munkaterület támogatja](concept-automated-ml.md) | 
| Adateltolódás észlelése | Nem támogatott | [Az SDK-ban és a Visual workspaceben támogatott](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Áttelepítés Machine Learning Studioról (klasszikus)

Jelenleg nincs mód a Studio (klasszikus) eszközök áttelepítésére Azure Machine Learning designerre (előzetes verzió). A jelenlegi Studio (klasszikus) felhasználók továbbra is használhatják a gépi tanulási eszközeiket. Azt javasoljuk azonban, hogy az összes felhasználót a tervező használatával fontolgatja, amely a továbbfejlesztett **munkafolyamatot, valamint a** méretezhetőséget, a verziókövetés és a nagyvállalati biztonságot is lehetővé teszi.

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés a Azure Machine Learning

A következő források segíthetnek a Azure Machine Learning megkezdésében. 

- Olvassa el a [Azure Machine learning áttekintést](overview-what-is-azure-ml.md).

- Hozza létre [első kísérletét a PYTHON SDK-val](tutorial-1st-experiment-sdk-setup.md).

- [Hozza létre első tervezői folyamatát](tutorial-designer-automobile-price-train-score.md) az automatikus árak előrejelzéséhez.

![Azure Machine Learning Designer – példa](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>További lépések

A tervezőben a fogd és vidd funkció mellett a Azure Machine Learning más eszközök is elérhetők:  
  + [Python-jegyzetfüzetek használata & ML-modellek üzembe helyezéséhez](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata & ML-modellek üzembe helyezéséhez](tutorial-1st-r-experiment.md) 
  + [Az automatizált gépi tanulás használata & ML-modellek üzembe helyezéséhez](tutorial-first-experiment-automated-ml.md)  
  + [Modellek betanítása és üzembe helyezése a Machine learning parancssori felület használatával](tutorial-train-deploy-model-cli.md)


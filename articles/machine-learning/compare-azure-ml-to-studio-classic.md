---
title: Azure Machine Learning vs. Machine Learning Studio (klasszikus)
description: Mi a különbség az Azure Machine Learning és a Machine Learning Studio (klasszikus) között?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371840"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (klasszikus)

Ebben a cikkben megtudhatja, hogy mi a különbség az Azure Machine Learning és a Machine Learning Studio (klasszikus) között. 

Az Azure Machine Learning python- és R-SDK-kat, **valamint** a "drag-and-drop" tervezőt biztosít a gépi tanulási modellek létrehozásához és üzembe helyezéséhez. Studio (klasszikus) csak kínál önálló drag-and-drop élményt.

Azt javasoljuk, hogy az új felhasználók válassza az Azure Machine Learning a legkorszerűbb gépi tanulási eszközök legszélesebb körét.

## <a name="quick-comparison"></a>Gyors összehasonlítás

Az alábbi táblázat összefoglalja az Azure Machine Learning és a Studio (klasszikus) közötti legfontosabb különbségeket:

| | Machine Learning Studio (klasszikus) | Azure Machine Learning |
|---| --- | --- |
| Húzási felület | Támogatott | Támogatott – [Azure Machine Learning-tervező (előzetes verzió)](concept-designer.md) | 
| Experiment | Méretezhető (10 GB-os betanítási adatkorlát) | Méretezés számítási céllal |
| Számítási célok betanítása | Saját számítási cél, csak a CPU-támogatás | Testre szabható [képzési számítási célok](concept-compute-target.md#train)széles választéka . Gpu- és CPU-támogatással | 
| Telepítési számítási célok | Saját webszolgáltatás-formátum, nem testreszabható | Testre szabható [telepítési számítási célok](concept-compute-target.md#deploy)széles választéka . Gpu- és CPU-támogatással |
| ML-es csővezeték | Nem támogatott | Rugalmas, moduláris [folyamatok létrehozása](concept-ml-pipelines.md) a munkafolyamatok automatizálásához |
| MLOps | Alapvető modellkezelés és -telepítés | Entitásverziók (modell, adatok, munkafolyamatok), munkafolyamat-automatizálás, integráció a CICD-eszközökkel [stb.](concept-model-management-and-deployment.md) |
| Modell formátuma | Saját formátum, Csak Stúdió (klasszikus) | Több támogatott formátum a képzési feladat típusától függően |
| Automatizált modellbetanítás és hiperparaméter-hangolás |  Nem támogatott | [Az SDK és a vizuális munkaterület támogatja](concept-automated-ml.md) | 
| Adateltolódás észlelése | Nem támogatott | [SDK-ban és vizuális munkaterületen támogatott](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Áttelepítés a Machine Learning Studióból (klasszikus)

Jelenleg nincs mód a Studio (klasszikus) eszközök Áttelepítésére az Azure Machine Learning designer (előzetes verzió). A jelenlegi Studio (klasszikus) felhasználók továbbra is használhatják a gépi tanulási eszközök. Azonban azt javasoljuk, hogy minden felhasználó, hogy fontolja meg a tervező, amely egy ismerős drag-and-drop élményt továbbfejlesztett **munkafolyamat, valamint** méretezhetőség, verziókövetés, és a vállalati biztonság.

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés az Azure Machine Learninggel

Az alábbi erőforrások segíthetnek az Azure Machine Learning megkezdésében. 

- Olvassa el az [Azure Machine Learning áttekintését.](overview-what-is-azure-ml.md)

- Hozza létre az [első kísérletet a Python SDK-val.](tutorial-1st-experiment-sdk-setup.md)

- [Hozza létre az első tervezői folyamatot](tutorial-designer-automobile-price-train-score.md) az automatikus árak előrejelzéséhez.

![Példa az Azure Machine Learning tervezőjének](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>További lépések

A tervező húzási képességei mellett az Azure Machine Learning más eszközökkel is rendelkezik:  
  + [Python-jegyzetfüzetek használata & ML-modellek üzembe helyezésének betanítása](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata & ml-modellek telepítésének betanításához](tutorial-1st-r-experiment.md) 
  + [Automatikus gépi tanulás használata az & üzembe helyezéséhez használt gépi tanulási modellek betanításához](tutorial-first-experiment-automated-ml.md)  
  + [A gépi tanulási CLI segítségével modellbe tanítható és helyezhet üzembe](tutorial-train-deploy-model-cli.md)


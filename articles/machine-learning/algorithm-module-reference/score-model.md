---
title: 'Pontszám modell: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Útmutató az Azure Machine Learning szolgáltatás a Score Model-modul használatával létrehozhat egy előrejelzéseket betanított besorolási vagy regressziós modell használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029265"
---
# <a name="score-model-module"></a>A Score model (Modell montozása) modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul segítségével hozza létre előrejelzések betanított osztályozási vagy regressziós modell használatával.

## <a name="how-to-use"></a>A használat módja

1. Adja hozzá a **Score Model** modult a kísérletvászonra.

2. Betanított modell és a egy új bemeneti adatokat tartalmazó adatkészlet csatolni. 

    Az adatok egy betanított modellt használ a típussal kompatibilis formátumban kell megadni. A bemeneti adatkészlet sémája általánosan is meg kell egyeznie a séma az adatok a modell betanításához használja.

3. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

Miután létrehozta a pontszámok használatával készletét [Score Model](./score-model.md):

+ A modell pontosságát (teljesítmény) értékeléséhez használt mérőszámok álló készletet hoz létre.  csatlakozhat a pontozott adatkészlet [Evaluate Model](./evaluate-model.md), 
+ Kattintson a jobb gombbal a modult, és válassza ki **Visualize** minta az eredmények megjelenítéséhez.
+ Mentse az eredményeket egy adatkészletet.

A pontszám, vagy az előre jelzett érték, a modell és a bemeneti adatok függően számos különböző formátumokban lehet:

- Képbesorolási modellek, a [Score Model](./score-model.md) előre jelzett érték az osztályt, valamint az előre jelzett érték valószínűségét adja vissza.
- Regressziós modellek [Score Model](./score-model.md) állít elő, csak az előre jelzett numerikus érték.
- A pontszám képbesorolási modellek, előfordulhat, hogy lehet az objektumosztályt, a képet, vagy olyan logikai érték, amely azt jelzi, hogy található-e egy adott funkció.

## <a name="publish-scores-as-a-web-service"></a>Eredmények közzététele webszolgáltatásként

A pontozás egyik gyakori felhasználási, hogy a hibaüzenettel reagál egy prediktív webszolgáltatás részeként. További információkért tekintse meg ebben az oktatóanyagban egy webszolgáltatás alapján az Azure Machine Learning-kísérlet létrehozása:


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 
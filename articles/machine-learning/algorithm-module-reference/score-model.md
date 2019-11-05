---
title: 'Pontszám modell: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki az előrejelzéseket egy betanított osztályozási vagy regressziós modell használatával a Azure Machine Learning pontszám modell moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e7ab953a7ac0907244ebaab70b3b86cbe6f0f4d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497637"
---
# <a name="score-model-module"></a>A Score model (Modell montozása) modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal a betanított besorolási vagy regressziós modell használatával készíthet előrejelzéseket.

## <a name="how-to-use"></a>A használat módja

1. Adja hozzá a **pontszám modell** modult a folyamathoz.

2. Csatoljon egy betanított modellt és egy új bemeneti adatokat tartalmazó adatkészletet. 

    Az adatformátumnak kompatibilisnek kell lennie az Ön által használt betanított modell típusával. A bemeneti adatkészlet sémájának általában egyeznie kell a modell betanításához használt adatok sémájával is.

3. A folyamat futtatása.

## <a name="results"></a>Results (Eredmények)

Miután létrehozta a pontszámok készletét a [score Model](./score-model.md)használatával:

+ A modell pontosságának értékeléséhez használt mérőszámok készletének létrehozása (teljesítmény).  a [modell kiértékeléséhez](./evaluate-model.md)összekapcsolhatók a pontozásos adatkészlet, 
+ Kattintson a jobb gombbal a modulra, és válassza a **Megjelenítés** lehetőséget az eredmények mintájának megtekintéséhez.
+ Mentse az eredményeket egy adatkészletbe.

A pontszám vagy az előre jelzett érték számos különböző formátumban lehet, a modelltől és a bemeneti adatoktól függően:

- A besorolási modellek esetében a [pontszám modell](./score-model.md) a osztály előre jelzett értékét, valamint az előre jelzett érték valószínűségét adja eredményül.
- A regressziós modellek esetében a [pontszám modell](./score-model.md) csak az előre jelzett numerikus értéket hozza létre.
- A képbesorolási modellek esetében a pontszám lehet a rendszerképben található objektum osztálya, vagy egy logikai érték, amely azt jelzi, hogy egy adott szolgáltatás található-e.

## <a name="publish-scores-as-a-web-service"></a>Pontszámok közzététele webszolgáltatásként

A pontozás gyakori használata a kimenet visszaküldése a prediktív webszolgáltatás részeként. További információkért tekintse meg ezt az oktatóanyagot, amely bemutatja, hogyan hozhat létre egy webszolgáltatást egy folyamaton alapuló Azure Machine Learningban:

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
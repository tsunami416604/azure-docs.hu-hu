---
title: 'Pontszám modell: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki az előrejelzéseket egy betanított osztályozási vagy regressziós modell használatával a Azure Machine Learning pontszám modell moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0cc367b86507d6d819608795851052e8a0c1c73b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890302"
---
# <a name="score-model"></a>Relevanciamodell

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal a betanított besorolási vagy regressziós modell használatával készíthet előrejelzéseket.

## <a name="how-to-use"></a>Használat

1. Adja hozzá a **pontszám modell** modult a folyamathoz.

2. Csatoljon egy betanított modellt és egy új bemeneti adatokat tartalmazó adatkészletet. 

    Az adatformátumnak kompatibilisnek kell lennie az Ön által használt betanított modell típusával. A bemeneti adatkészlet sémájának általában egyeznie kell a modell betanításához használt adatok sémájával is.

3. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

Miután létrehozta a pontszámok készletét a [score Model](./score-model.md)használatával:

+ A modell pontosságának (teljesítményének) kiértékeléséhez használt mérőszámok előállításához összekapcsolhatók a [modell kiértékelésére](./evaluate-model.md)szolgáló pontozásos adatkészlet, 
+ Kattintson a jobb gombbal a modulra, és válassza a **Megjelenítés** lehetőséget az eredmények mintájának megtekintéséhez.
<!-- + To Save the results to a dataset. -->

A pontszám vagy az előre jelzett érték számos különböző formátumban lehet, a modelltől és a bemeneti adatoktól függően:

- A besorolási modellek esetében a [pontszám modell](./score-model.md) a osztály előre jelzett értékét, valamint az előre jelzett érték valószínűségét adja eredményül.
- A regressziós modellek esetében a [pontszám modell](./score-model.md) csak az előre jelzett numerikus értéket hozza létre.


## <a name="publish-scores-as-a-web-service"></a>Pontszámok közzététele webszolgáltatásként

A pontozás gyakori használata a kimenet visszaküldése a prediktív webszolgáltatás részeként. További információkért tekintse meg [ezt az oktatóanyagot](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) , amely bemutatja, hogyan helyezhet üzembe egy valós idejű végpontot a Azure Machine learning Designer egyik folyamata alapján.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
---
title: 'Pontszámmodell: Modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Score Model modult az Azure Machine Learningben egy betanított besorolási vagy regressziós modell használatával előrejelzések létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364175"
---
# <a name="score-model-module"></a>A Score model (Modell montozása) modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal előrejelzéseket hozhat létre egy betanított besorolási vagy regressziós modell használatával.

## <a name="how-to-use"></a>A használat módja

1. Adja hozzá a **Score Model** modult a folyamathoz.

2. Csatolt modell és új bemeneti adatokat tartalmazó adatkészlet csatolása. 

    Az adatoknak a használt betanított modell típusával kompatibilis formátumban kell lenniük. A bemeneti adatkészlet sémájának általában meg kell egyeznie a modell betanításához használt adatok sémájával is.

3. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

Miután létrehozott egy sor pontszámok segítségével [Score Model:](./score-model.md)

+ A modell pontosságának (teljesítményének) kiértékeléséhez használt mérőszámok készletének létrehozásához csatlakoztassa a pontozott adatkészletet a [Modell kiértékelése ,](./evaluate-model.md) 
+ Kattintson a jobb gombbal a modulra, és válassza a **Visualize parancsot** az eredmények mintájának megtekintéséhez.
<!-- + To Save the results to a dataset. -->

A pontszám vagy az előre jelzett érték a modelltől és a bemeneti adatoktól függően számos különböző formátumban lehet:

- Besorolási modellek esetében [a Score Model](./score-model.md) egy előre jelzett értéket ad ki az osztályhoz, valamint az előre jelzett érték valószínűségét.
- Regressziós modellek esetén [a score modell](./score-model.md) csak az előre jelzett numerikus értéket hozza létre.


## <a name="publish-scores-as-a-web-service"></a>Pontszámok közzététele webszolgáltatásként

A pontozási gyakori használata a kimenet egy prediktív webszolgáltatás részeként. További információkért tekintse meg [az oktatóanyagot,](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) amely bemutatja, hogyan telepíthet valós idejű végpontot egy folyamat az Azure Machine Learning-tervezőben.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
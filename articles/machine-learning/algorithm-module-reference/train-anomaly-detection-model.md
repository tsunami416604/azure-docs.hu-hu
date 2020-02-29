---
title: 'A Train rendellenesség észlelési modellje: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy betanított anomália-észlelési modellt a Train anomália-észlelési modell modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 55c5b89eb23581543d57187ee4d7899d97eafe66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925955"
---
# <a name="train-anomaly-detection-model"></a>Tanítási rendellenesség észlelési modellje

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designer (előzetes verzió) **Train rendellenesség-észlelési modell** modulja egy betanított anomália-észlelési modell létrehozásához.

A modul a modell paramétereinek egy készletét adja meg a rendellenesség-észlelési modellhez és egy címkézetlen adatkészlethez. Egy betanított anomália-észlelési modellt ad vissza, amely a betanítási adatokhoz tartozó címkék készletével együtt.  

A tervezőben elérhető anomália-észlelési algoritmusokkal kapcsolatos további információkért tekintse meg a következő témaköröket: 

+ [PCA-alapú anomáliák észlelése](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>A Train anomália észlelési modell konfigurálása 

1.  Adja hozzá a **vonat anomália észlelési modell** modult a folyamathoz a tervezőben. Ez a modul az **anomáliák észlelése** kategóriában található.

2. A anomáliák észlelésére tervezett modulok egyikének összekötése, például a [PCA-alapú anomáliák észlelése](pca-based-anomaly-detection.md)

    Más típusú modellek nem támogatottak; a folyamat futtatásakor a következő hibaüzenet jelenik meg: minden modellnek ugyanazzal a tanulói típussal kell rendelkeznie.  

3.  Konfigurálja az anomáliák észlelése modult a Label (címke) oszlop kiválasztásával, és az algoritmushoz tartozó egyéb paraméterek megadásával.  

4.  Csatoljon egy betanítási adatkészletet a **vonat anomália észlelési modell**jobb oldali bemenetéhez.  

5.  A folyamat futtatása.  

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ A modell paramétereinek megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés**lehetőséget. 

+ Az előrejelzések létrehozásához használja a [score modelt](score-model.md) új bemeneti adatokkal.

+ A betanított modell pillanatképének mentéséhez válassza ki a modult, majd kattintson az **adatkészlet regisztrálása** ikonra a jobb oldali panel **outputs + naplók** lapján.   

 
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

A Designer modulokra jellemző hibák listáját [a tervező (előzetes verzió) kivételei és hibakódai](designer-error-codes.md) részben tekintheti meg.

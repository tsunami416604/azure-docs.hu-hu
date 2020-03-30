---
title: 'Vonatfürtözési modell: modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a fürtözési modell betanítása modul az Azure Machine Learning fürtözési modellek betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477425"
---
# <a name="train-clustering-model"></a>Csoportosítási modell betanítása

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal betanítsa a fürtözési modellt.

A modul egy nem képzetlen fürtözési modellt vesz igénybe, amelyet már konfigurált a [K-Means fürtözési](k-means-clustering.md) modul használatával, és betanítja a modellt egy címkézett vagy címkézetlen adatkészlet használatával. A modul létrehoz egy betanított modellt, amely előrejelzéshez használható, és fürthozzárendeléseket a betanítási adatok minden egyes esetéhez.

> [!NOTE]
> A fürtözési modell nem tanítható be a [Train Model](train-model.md) modul, amely az általános modul a gépi tanulási modellek betanításához. Ennek az az [oka, hogy a Train Model](train-model.md) csak felügyelt tanulási algoritmusokkal működik. A K-eszközök és más fürtözési algoritmusok lehetővé teszik a felügyelet nélküli tanulást, ami azt jelenti, hogy az algoritmus tanulhat a címkézetlen adatokból.  
  
## <a name="how-to-use-train-clustering-model"></a>A vonatfürtözési modell használata  

1.  Adja hozzá a **Train Fürtözési modell** modult a tervező ben lévő folyamathoz. A modul a **Machine Learning Modules**( A **vonat** kategória) alatt található.  
  
2. Adja hozzá a [K-Means fürtözési modult,](k-means-clustering.md) vagy egy másik egyéni modult, amely kompatibilis fürtözési modellt hoz létre, és állítsa be a fürtözési modell paramétereit.  
    
3.  Betanítási adatkészlet csatolása a **vonatfürtözési modell**jobb oldali bemenetéhez.
  
5.  Az **Oszlopkészlet mezőben**jelölje ki az adatkészlet ből a fürtök létrehozásához használni kívánt oszlopokat. Ügyeljen arra, hogy olyan oszlopokat jelöljön ki, amelyek jó tulajdonságokat tesznek ki: például kerülje az egyedi értékekkel rendelkező azonosítók vagy oszlopok használatát, vagy az okat azonos értékű oszlopokat.

    Ha egy címke elérhető, használhatja azt funkcióként, vagy kihagyhatja.  
  
6. Válassza ki a beállítást, **A hozzáfűzés ellenőrzése vagy törölje a jelet csak az eredményhez,** ha a betanítási adatokat az új fürtcímkével együtt szeretné kiadni.

    Ha nem jelöli be ezt a beállítást, csak a fürt-hozzárendelések lesznek kimenetben. 

7. Küldje el a folyamatot, vagy kattintson a **Fürttípus betanítása modulra,** és válassza a **Kijelölt futtatás lehetőséget.**  
  
### <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.

+ Ha pontszámokat szeretne létrehozni a modellből, használja [az Adatok hozzárendelése fürtökhöz parancsot.](assign-data-to-clusters.md)

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
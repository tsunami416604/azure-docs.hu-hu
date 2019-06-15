---
title: 'Betanítását, fürtszolgáltatási modell: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a fürtözési modell betanításához modul az Azure Machine Learning szolgáltatás fürtözési modelleket taníthat be.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028080"
---
# <a name="train-clustering-model"></a>Csoportosítási modell betanítása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával a fürtözési modell betanításához.

A modul veszi egy kellő fürtözési modell használata már konfigurálta a [K-közép-Fürtszolgáltatás](k-means-clustering.md) modult, és betanítja a modellt címkézett vagy címke nélküli adatkészlet használatával. A modul hoz létre a mindkét betanított modell előrejelzési, és beállított egy fürt hozzárendelését a betanítási adatok minden esetben használható.

> [!NOTE]
> A fürtözési modell nem lehet tanítása az a [tanítási modell](train-model.md) modult, amely az általános modul machine learning-modellek betanításához. Ez azért van, [tanítási modell](train-model.md) működik csak a felügyelt tanulási algoritmus. K-közép- és más fürtözési algoritmusok engedélyezése felügyeletlen tanulás, ami azt jelenti, hogy az algoritmus tudhat meg a címke nélküli adatokból.  
  
## <a name="how-to-use-train-clustering-model"></a>Fürtszolgáltatás tanítási használata  
  
1.  Adja hozzá a **Train-csoportosítási modellben** modult a kísérletvászonra a Studióban. A modul alatt találja **Machine Learning-modulok**, a a **Train** kategória.  
  
2. Adja hozzá a [K-közép-Fürtszolgáltatás](k-means-clustering.md) modul, vagy egy másik egyéni modult, amely létrehoz egy kompatibilis fürtszolgáltatás modellezheti, és állítsa be a paramétereket a fürtözési modell.  
    
3.  Betanítási adatkészletet csatolása a jobb oldali bemeneti portjával **Train-csoportosítási modellben**.
  
5.  A **oszlopkészlet**, válassza ki az oszlopokat az adatkészletből a fürt létrehozásakor használt. Ügyeljen arra, hogy válassza ki az oszlopot, amely jó szolgáltatásait: Kerülje például az azonosítók vagy más egyedi értékkel rendelkező oszlopot vagy oszlopokat, amelyek ugyanazokat az értékeket.

    Egy címke nem érhető el, ha szeretné használni a szolgáltatást, vagy hagyja.  
  
6. A beállítást, **ellenőrizze az eredmény csak hozzáfűzéssel bővíthető vagy négyzet jelölését**, ha azt szeretné, a kimenetben a betanítási adatok, és az új fürt címke.

    Törölje ezt a beállítást, ha csak a fürt-hozzárendeléseket a kimeneti. 

7. Futtassa a kísérletet, vagy kattintson a **Train-csoportosítási modellben** modul, és válassza ki **kijelölt futtatása**.  
  
### <a name="results"></a>Results (Eredmények)

Miután befejeződött a képzés:


+  Tekintse meg az értékeket az adatkészletben, kattintson a jobb gombbal a modult, válassza **adatkészletek eredmény**, és kattintson a **Visualize**.

+ A betanított modell újabb újra felhasználhatja a menteni, kattintson a jobb gombbal a modult, jelölje ki **Trained model**, és kattintson a **betanított modell mentése másként**.

+ Pontszámok készítése a modellből, használja a [adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md).



## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 
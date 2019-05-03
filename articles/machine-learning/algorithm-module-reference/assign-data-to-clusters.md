---
title: 'Adatok hozzárendelése fürt: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Útmutató az Azure Machine Learning szolgáltatás az adatok hozzárendelése fürt modul használatával a fürtözési modell pontozása.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028770"
---
# <a name="assign-data-to-clusters"></a>Adatok hozzárendelése fürtökhöz

Ez a cikk ismerteti, hogyan használható a [adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md) modul vizuális felületen, a létrehozásához, amely a K-közép-csoportosítási algoritmus használatával lett betanított fürtözési modell segítségével előrejelzéseket.

A modul egy adatkészletet, amely tartalmazza minden egyes új adatpont valószínű hozzárendeléseinek adja vissza. 


## <a name="how-to-use-assign-data-to-clusters"></a>Hogyan használható az adatok hozzárendelése fürtökhöz
  
1.  Vizuális felhasználói felületet keresse meg a fürtözési korábban betanított modell. Hozzon létre, és a fürtözési modell betanításához az alábbi módszerek egyikével:  
  
    - A K-közép algoritmus használatával konfigurálja a [K-közép-Fürtszolgáltatás](k-means-clustering.md) modul és a modell segítségével az adatkészlet majd train és a [fürtözési modell betanításához](train-clustering-model.md) modul.  
  
  
    Is hozzáadhat egy meglévő betanított fürtözési modell a a **mentett modellek** csoporthoz a munkaterületét.

2. A bal oldali bemeneti portjával csatolja a betanított modell [adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md).  

3. Egy új adatkészlet csatolása bemenetként. Ez az adatkészlet a címkék nem kötelező. Általában a fürtszolgáltatás egy olyan felügyeletlen learning módszer, nem valószínű, hogy meg fog ismerni kategóriák.

    Azonban a bemeneti oszlopok azonosnak kell lennie a fürtözési modell, vagy hiba történt a betanítási rendszerben használt oszlopok szerepelnek.

    > [!TIP]
    > A kimeneti oszlopok számának csökkentése a fürt előrejelzéseket, használja a [Select Columns in Dataset](select-columns-in-dataset.md), és válassza ki az oszlopok egy része. 
    
4. Hagyja bejelölve az **ellenőrizze az eredmény csak hozzáfűzéssel bővíthető vagy négyzet jelölését** kiválasztva, ha azt szeretné, hogy a teljes adatkészletet, egy olyan oszlop jelzi az eredményeket (fürt hozzárendelések) együtt tartalmazza az eredményeket.
  
    Törölje ezt a beállítást, ha vissza csak az eredményeket. Ez hasznos lehet, amikor egy webszolgáltatás részeként előrejelzések létrehozását.
  
5.  Futtassa a kísérletet.  
  
### <a name="results"></a>Results (Eredmények)

 
+  Tekintse meg az értékeket az adatkészletben, kattintson a jobb gombbal a modult, válassza **adatkészletek eredmény**, és kattintson a **Visualize**.


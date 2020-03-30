---
title: 'Adatok hozzárendelése fürthöz: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adatok hozzárendelése a fürthöz modul tanait az Azure Machine Learning ben a fürtözési modell pontzatához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477714"
---
# <a name="module-assign-data-to-clusters"></a>Modul: Adatok hozzárendelése fürtökhöz

Ez a cikk ismerteti, hogyan használhatja az *adatok hozzárendelése fürtökhöz* modul az Azure Machine Learning designer (előzetes verzió). A modul előrejelzéseket hoz létre egy fürtözési modellen keresztül, amely a *K-means fürtözési* algoritmussal lett betanítva.

Az Adatok hozzárendelése a fürtökhöz modul egy adatkészletet ad vissza, amely tartalmazza az egyes új adatpontokkal való valószínű hozzárendeléseket. 

## <a name="how-to-use-assign-data-to-clusters"></a>Adatok hozzárendelése fürtökhöz használata
  
1. Az Azure Machine Learning-tervezőben keresse meg a korábban betanított fürtözési modellt. Fürtkezelési modellt az alábbi módszerek egyikével hozhat létre és taníthat be:  
  
    - Konfigurálja a K-means fürtözési algoritmust a [K-Means fürtözési](k-means-clustering.md) modul használatával, és tanítsa be a modellt egy adatkészlet és a Fürtözési modell betanítása modul (ez a cikk) használatával.  
  
    - A munkaterület **mentett modellek** csoportjából is hozzáadhat egy meglévő betanított fürtözési modellt.

2. Csatlakoztassa a betanított modellt az **Adatok hozzárendelése fürtökhöz**bal oldali bemeneti portjához.  

3. Új adatkészlet csatolása bemenetként. 

   Ebben az adatkészletben a címkék nem kötelezőek. Általában a fürtözés egy felügyelet nélküli tanulási módszer. Nem várható, hogy előre ismerje a kategóriákat. A bemeneti oszlopoknak azonban meg kell egyeznek a fürtözési modell betanításához használt oszlopokkal, vagy hiba történik.

    > [!TIP]
    > A fürtelőrejelzésekből a tervezőnek írt oszlopok számának csökkentéséhez használja [az Oszlopok kiválasztása az adatkészletben](select-columns-in-dataset.md)lehetőséget, és jelölje ki az oszlopok egy részét. 
    
4. Ha azt szeretné, hogy az eredmények tartalmazzák a teljes bemeneti adatkészletet, az eredményeket is beleértve az eredményeket (fürt-hozzárendeléseket) tartalmazó oszlopot is, jelölje be a Csak **eredményre jelölőnégyzetet, vagy törölje a jelet** az eredmény jelölőnégyzetből.
  
    Ha törli a jelet a jelölőnégyzetből, csak az eredmények jelennek meg. Ez a beállítás akkor lehet hasznos, ha egy webszolgáltatás részeként előrejelzéseket hoz létre.
  
5.  Küldje el a folyamatot.  
  
### <a name="results"></a>Results (Eredmények)

+  Az adatkészlet értékeinek megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés parancsot.** Vagy Válassza ki a **modult, és váltson** a jobb oldali panel Kimenetek fülére, kattintson a hisztogram ikonra a **Port kimenetekben** az eredmény megjelenítéséhez.


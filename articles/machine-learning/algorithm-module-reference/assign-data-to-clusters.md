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
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467200"
---
# <a name="module-assign-data-to-clusters"></a>A modul: Adatok hozzárendelése fürtökhöz

Ez a cikk ismerteti, hogyan használható a *adatok hozzárendelése fürtökhöz* modul az Azure Machine Learning vizuális felületen. A modul állít elő, előrejelzéseket volt képzett csoportosítási modellben a *K-közép-Fürtszolgáltatás* algoritmus.

A fürtök modul hozzárendelése adatokat adja vissza egy adatkészletet, amely tartalmazza az új adatpontok valószínű hozzárendelések. 


## <a name="how-to-use-assign-data-to-clusters"></a>Hogyan használható az adatok hozzárendelése fürtökhöz
  
1. Az Azure Machine Learning vizuális felhasználói felületet keresse meg a fürtözési korábban betanított modell. Hozzon létre, és a fürtözési modell betanításához az alábbi módszerek egyikével:  
  
    - Konfigurálja a K-közép-csoportosítási algoritmus használatával a [K-közép-Fürtszolgáltatás](k-means-clustering.md) modul és train a modellben egy adatkészletet, és a fürtözési modell betanításához modul (Ez a cikk) használatával.  
  
    - Is hozzáadhat egy meglévő betanított fürtözési modell a a **mentett modellek** csoporthoz a munkaterületét.

2. A bal oldali bemeneti portjával csatolja a betanított modell **adatok hozzárendelése fürtökhöz**.  

3. Egy új adatkészlet csatolása bemenetként. 

   Ez az adatkészlet a címkék nem kötelező. Általában a fürtszolgáltatás egy olyan felügyeletlen learning módszer. Nem várt már ismerni a kategóriák. Azonban a bemeneti oszlopok azonosnak kell lennie a fürtözési modell, vagy hiba történt a betanítási rendszerben használt oszlopok szerepelnek.

    > [!TIP]
    > A felület, a fürt előrejelzéseket írt oszlopok számának csökkentése, használja a [oszlopok kiválasztása az adatkészlet](select-columns-in-dataset.md), és válassza ki az oszlopok egy része. 
    
4. Hagyja a **ellenőrizze az eredmény csak hozzáfűzéssel bővíthető vagy négyzet jelölését** jelölőnégyzet be van jelölve, ha azt szeretné, hogy az eredményeket a teljes adatkészletet, beleértve egy oszlopot, amely megjeleníti az eredményeket (fürt hozzárendelések) tartalmaznak.
  
    Ha törli ezt a jelölőnégyzetet, a rendszer csak az eredményeket adja vissza. Ez a beállítás akkor lehet hasznos, előrejelzéseket egy webes szolgáltatás részeként történő létrehozásakor.
  
5.  Futtassa a kísérletet.  
  
### <a name="results"></a>Results (Eredmények)

+  Tekintse meg az értékeket az adatkészletben, kattintson a jobb gombbal a modult, jelölje ki **adatkészletek eredmény**, majd válassza ki **Visualize**.


---
title: 'Fürtszolgáltatási modell betanítása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Service-ben a vonat-fürtszolgáltatási modell modult a fürtözési modellek betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 4883b1420913eb4e5f3bd5f13a95e410370d9184
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128479"
---
# <a name="train-clustering-model"></a>Csoportosítási modell betanítása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ez a modul egy fürtszolgáltatási modell betanítására használható.

A modul olyan nem betanított fürtszolgáltatási modellt vesz igénybe, amelyet már konfigurált a [K-Meaning-fürtszolgáltatási](k-means-clustering.md) modullal, és a modellt a címkével ellátott vagy nem címkézett adatkészletet használva. A modul létrehoz egy betanított modellt is, amelyet az előrejelzéshez használhat, valamint a betanítási adatokban lévő egyes esetekben a fürt hozzárendeléseinek készletét.

> [!NOTE]
> A adatbázismotor a betanítási modell modul használatával [](train-model.md) tanítjuk ki, amely a gépi tanulási modellek általános modulja. Ennek oka, hogy a betanítási [modell](train-model.md) csak felügyelt tanulási algoritmusokkal működik. A K-means és más fürtözési algoritmusok lehetővé teszik a nem felügyelt tanulást, ami azt jelenti, hogy az algoritmus nem címkézett adatokból tanulhat.  
  
## <a name="how-to-use-train-clustering-model"></a>A vonat-fürtszolgáltatási modell használata  
  
1.  Adja hozzá a **Train fürtözési modell** modult a kísérlethez a Studióban. A modult **Machine learning modulok**területen találja a **vonat** kategóriában.  
  
2. Adja hozzá a [K-means fürtszolgáltatási](k-means-clustering.md) modult, vagy egy másik olyan egyéni modult, amely kompatibilis fürtszolgáltatási modellt hoz létre, és állítsa be a fürtszolgáltatási modell paramétereit.  
    
3.  Csatoljon egy betanítási adatkészletet a **vonat-fürtözési modell**jobb oldali bemenetéhez.
  
5.  Az **oszlop beállítása**területen válassza ki a fürtök létrehozásához használni kívánt adatkészlet oszlopait. Ügyeljen arra, hogy olyan oszlopokat válasszon ki, amelyek jó szolgáltatásokat tesznek elérhetővé: például ne használjon azonosítókat vagy más olyan oszlopokat, amelyek egyedi értékekkel rendelkeznek, vagy az összes azonos értékkel rendelkező oszlopokat.

    Ha egy címke elérhető, használhatja szolgáltatásként, vagy hagyja ki.  
  
6. Jelölje be a lehetőséget, ha a betanítási adatokat az új fürt címkével együtt szeretné kiadni, válassza a **Hozzáfűzés vagy a kijelölés jelölőnégyzetet**.

    Ha kijelöli ezt a beállítást, csak a fürt hozzárendelései lesznek kimenetek. 

7. Futtassa a kísérletet, vagy kattintson a **vonat-fürtszolgáltatási modell** modulra, és válassza a **kijelölt futtatása**lehetőséget.  
  
### <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:


+  Az adatkészlet értékeinek megtekintéséhez kattintson a jobb gombbal a modulra, válassza az **eredmény**-adatkészletek lehetőséget, majd kattintson a **Megjelenítés**elemre.

+ A betanított modell későbbi újrafelhasználásához kattintson a jobb gombbal a modulra, válassza a betanított **modell**lehetőséget, majd kattintson a **Mentés**betanított modellként lehetőségre.

+ A modellből származó pontszámok létrehozásához használja az [adatok](assign-data-to-clusters.md)kiosztása fürtökhöz lehetőséget.



## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
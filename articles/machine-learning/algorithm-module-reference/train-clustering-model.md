---
title: 'A szerelvények csoportosítási modellje: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning vonat-fürtszolgáltatási modell modult a fürtözési modellek betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: ea247af871b06bd21c2733272b3d938c536d16ec
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546452"
---
# <a name="train-clustering-model"></a>Csoportosítási modell betanítása

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ez a modul egy fürtszolgáltatási modell betanítására használható.

A modul olyan nem betanított fürtszolgáltatási modellt vesz igénybe, amelyet már konfigurált a [K-Meaning-fürtszolgáltatási](k-means-clustering.md) modullal, és a modellt a címkével ellátott vagy nem címkézett adatkészletet használva. A modul létrehoz egy betanított modellt is, amelyet az előrejelzéshez használhat, valamint a betanítási adatokban lévő egyes esetekben a fürt hozzárendeléseinek készletét.

> [!NOTE]
> A adatbázismotor a [betanítási](train-model.md) modell modul használatával tanítjuk ki, amely a gépi tanulási modellek általános modulja. Ennek oka, hogy a [betanítási modell](train-model.md) csak felügyelt tanulási algoritmusokkal működik. A K-means és más fürtözési algoritmusok lehetővé teszik a nem felügyelt tanulást, ami azt jelenti, hogy az algoritmus nem címkézett adatokból tanulhat.  
  
## <a name="how-to-use-train-clustering-model"></a>A vonat-fürtszolgáltatási modell használata  

1.  Adja hozzá a **Train fürtözési modell** modult a folyamathoz a tervezőben. A modult **Machine learning modulok**területen találja a **vonat** kategóriában.  
  
2. Adja hozzá a [K-means fürtszolgáltatási](k-means-clustering.md) modult, vagy egy másik olyan egyéni modult, amely kompatibilis fürtszolgáltatási modellt hoz létre, és állítsa be a fürtszolgáltatási modell paramétereit.  
    
3.  Csatoljon egy betanítási adatkészletet a **vonat-fürtözési modell**jobb oldali bemenetéhez.
  
5.  Az **oszlop beállítása**területen válassza ki a fürtök létrehozásához használni kívánt adatkészlet oszlopait. Ügyeljen arra, hogy olyan oszlopokat válasszon ki, amelyek jó szolgáltatásokat tesznek elérhetővé: például ne használjon azonosítókat vagy más olyan oszlopokat, amelyek egyedi értékekkel rendelkeznek, vagy az összes azonos értékkel rendelkező oszlopokat.

    Ha egy címke elérhető, használhatja szolgáltatásként, vagy hagyja ki.  
  
6. Jelölje be a lehetőséget, ha a betanítási adatokat az új fürt címkével együtt szeretné kiadni, válassza a **Hozzáfűzés vagy a kijelölés jelölőnégyzetet**.

    Ha kijelöli ezt a beállítást, csak a fürt hozzárendelései lesznek kimenetek. 

7. Futtassa a folyamatot, vagy kattintson a **vonat-fürtszolgáltatási modell** modulra, és válassza a **kijelölt futtatása**lehetőséget.  
  
### <a name="results"></a>Eredmények

A betanítás befejezése után:

+ Ha menteni szeretné a betanított modell pillanatképét, válassza a **kimenetek** fület a **vonat modell** moduljának jobb oldali paneljén. Válassza az **adatkészlet regisztrálása** ikont a modell újrafelhasználható modulként való mentéséhez.

+ A modellből származó pontszámok létrehozásához használja az [adatok kiosztása fürtökhöz](assign-data-to-clusters.md)lehetőséget.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
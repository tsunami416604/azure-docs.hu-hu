---
title: 'Átalakítás alkalmazása: Modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Átalakítás modul az Azure Machine Learning egy bemeneti adatkészlet módosítása egy korábban kiszámított átalakítás alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395263"
---
# <a name="apply-transformation-module"></a>Átalakítás modul alkalmazása

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal egy korábban kiszámított átalakításon alapuló bemeneti adatkészletet módosíthat.

Ha például z-pontszámokat használt a betanítási adatok normalizálásához az **Adatok normalizálása** modul használatával, akkor a z-score értéket is használni szeretné, amelyet a pontozási fázisban a betanításhoz számított. Az Azure Machine Learningben mentheti a normalizálási módszert átalakításként, majd **az Átalakítás alkalmazása** használatával alkalmazhatja a z-pontszám a bemeneti adatokra a pontozás előtt.

## <a name="how-to-save-transformations"></a>Az átalakítások mentése

A tervező lehetővé teszi az adatátalakítások **adathalmazként** történő mentését, így más folyamatokban is használhatóak.

1. Válasszon ki egy sikeresen futtatott adatátalakítási modult.

1. Válassza a **Kimenetek + naplók** lapot.

1. Az **Eredményátalakítás**mentéséhez kattintson a **Mentés ikonra.**

## <a name="how-to-use-apply-transformation"></a>Az Átalakítás alkalmazása használata  
  
1. Adja hozzá az **Átalakítás alkalmazása** modult a folyamathoz. Ezt a modult a modulpaletta **Modellpontozás & kiértékelés** szakaszában találja. 
  
1. Keresse meg a használni kívánt mentett átalakítást a modulpaletta **Adatkészleteim** > **adatkészletei** alatt.

1. Csatlakoztassa a mentett átalakítás kimenetét az **Átalakítás alkalmazása** modul bal oldali bemeneti portjához.

    Az adatkészletnek pontosan ugyanolyan sémával kell rendelkeznie (oszlopok száma, oszlopnevek, adattípusok), mint az az adatkészlet, amelyhez az átalakítást először tervezték.  
  
1. Csatlakoztassa a kívánt modul adatkészletkimenetét az Átalakítás alkalmazása modul megfelelő **bemeneti** portjához.
  
1. Ha átalakítást szeretne alkalmazni az új adatkészletre, futtassa a folyamatot.  

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
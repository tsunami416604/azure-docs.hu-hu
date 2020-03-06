---
title: 'Átalakítás alkalmazása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning átalakítási modulját a bemeneti adatkészlet módosítására egy korábban számított átalakítás alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395263"
---
# <a name="apply-transformation-module"></a>A alkalmazni átalakítási modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal módosíthatja a bemeneti adatkészletet egy korábban számított transzformáció alapján.

Ha például a z-score-t használta a betanítási adatok normalizálása érdekében a **normalizálás** adatmodul használatával, akkor azt a z-score értéket érdemes használni, amelyet a program a pontozási fázisban is kiszámított képzéshez. Azure Machine Learning a normalizálás módszerét átalakíthatja átalakításként, majd az **átalakítás alkalmazása** paranccsal alkalmazhatja a z-pontszámot a bemeneti adatokra a pontozás előtt.

## <a name="how-to-save-transformations"></a>Átalakítások mentése

A Designer lehetővé teszi adatátalakítások **adatkészletként** való mentését, így azokat más folyamatokban is használhatja.

1. Válassza ki azt az Adatátalakítási modult, amely sikeresen futott.

1. Válassza a **kimenetek + naplók** lapot.

1. Válassza a **Mentés ikont** az **eredmény átalakításának**mentéséhez.

## <a name="how-to-use-apply-transformation"></a>Az átalakítás alkalmazása  
  
1. Adja hozzá az **átalakítási modul alkalmazása** a folyamathoz lehetőséget. Ez a modul a modul paletta **modell pontozás & próbaverzió** szakaszában található. 
  
1. Keresse meg a modul palettáján az adatkészletek ** > adatkészletek** alatt használni kívánt mentett átalakítást.

1. A mentett transzformáció kimenetének összekötése az **átalakítási modul alkalmazása** bal oldali bemeneti portjával.

    Az adatkészletnek pontosan ugyanazzal a sémával kell rendelkeznie (oszlopok száma, oszlopnevek, adattípusok), mint azt az adatkészletet, amelyhez az átalakítást először tervezték.  
  
1. Kapcsolja össze a kívánt modul adatkészlet-kimenetét az **átalakítási** modul megfelelő bemeneti portjával.
  
1. Ha egy átalakítást szeretne alkalmazni az új adatkészletre, futtassa a folyamatot.  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
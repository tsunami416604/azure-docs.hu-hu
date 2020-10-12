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
ms.date: 06/05/2020
ms.openlocfilehash: 7573abbbee479bfb0d1710beba3b95d084a5e657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898880"
---
# <a name="apply-transformation-module"></a>Átalakítási modul alkalmazása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal módosíthatja a bemeneti adatkészletet egy korábban számított transzformáció alapján.

Ha például a z-score-t használta a betanítási adatok normalizálása érdekében a **normalizálás** adatmodul használatával, akkor azt a z-score értéket érdemes használni, amelyet a program a pontozási fázisban is kiszámított képzéshez. Azure Machine Learning a normalizálás módszerét átalakíthatja átalakításként, majd az **átalakítás alkalmazása** paranccsal alkalmazhatja a z-pontszámot a bemeneti adatokra a pontozás előtt.

## <a name="how-to-save-transformations"></a>Átalakítások mentése

A Designer lehetővé teszi adatátalakítások **adatkészletként** való mentését, így azokat más folyamatokban is használhatja.

1. Válassza ki azt az Adatátalakítási modult, amely sikeresen futott.

1. Válassza a **kimenetek + naplók** lapot.

1. Keresse meg az átalakítás kimenetét, és válassza az **adatkészlet regisztrálása** lehetőséget, hogy a modul paletta **adatkészletek** kategóriája alatt mentse modulként.

## <a name="how-to-use-apply-transformation"></a>Az átalakítás alkalmazása  
  
1. Adja hozzá az **átalakítási modul alkalmazása** a folyamathoz lehetőséget. Ez a modul a modul paletta **modell pontozás & próbaverzió** szakaszában található. 
  
1. Keresse meg a modul paletta **adatkészletek** területén használni kívánt mentett átalakítást.

1. A mentett transzformáció kimenetének összekötése az **átalakítási modul alkalmazása** bal oldali bemeneti portjával.

    Az adatkészletnek pontosan ugyanazzal a sémával kell rendelkeznie (oszlopok száma, oszlopnevek, adattípusok), mint azt az adatkészletet, amelyhez az átalakítást először tervezték.  
  
1. Kapcsolja össze a kívánt modul adatkészlet-kimenetét az **átalakítási** modul megfelelő bemeneti portjával.
  
1. Ha egy átalakítást szeretne alkalmazni az új adatkészletre, futtassa a folyamatot.  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
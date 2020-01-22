---
title: 'Átalakítás alkalmazása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning átalakítási modulját a bemeneti adatkészlet módosítására egy korábban számított átalakítás alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a48ce60dca9f4221e364d53567f5b53719deb18c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314521"
---
# <a name="apply-transformation-module"></a>Átalakítási modul alkalmazása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal módosíthatja a bemeneti adatkészletet egy korábban számított transzformáció alapján.  
  
Ha például a z-score-t használta a betanítási adatok normalizálása érdekében a **normalizálás** adatmodul használatával, akkor azt a z-score értéket érdemes használni, amelyet a program a pontozási fázisban is kiszámított képzéshez. Azure Machine Learning a normalizálás módszerét átalakíthatja átalakításként, majd az **átalakítás alkalmazása** paranccsal alkalmazhatja a z-pontszámot a bemeneti adatokra a pontozás előtt.
  
Azure Machine Learning támogatást nyújt számos különböző egyéni átalakítás létrehozásához, majd alkalmazásához. Előfordulhat például, hogy menteni szeretné, majd újrahasznosítja az átalakításokat a következőre:  
  
- Hiányzó értékek eltávolítása vagy cseréje a **tiszta hiányzó adatok** használatával
- Adatnormalizálás az **Adatnormalizálás** használatával
  

## <a name="how-to-use-apply-transformation"></a>Az átalakítás alkalmazása  
  
1. Adja hozzá az **átalakítási modul alkalmazása** a folyamathoz lehetőséget. Ez a modul a **pontszám** kategóriában **Machine learning**alatt található. 
  
2. Keressen egy meglévő átalakítást bemenetként való használatra.  A korábban mentett átalakítások a bal oldali navigációs ablaktábla **átalakítások** csoportjában találhatók.  
  
   
  
3. Az átalakítani kívánt adatkészlet csatlakoztatása. Az adatkészletnek pontosan ugyanazzal a sémával kell rendelkeznie (oszlopok száma, oszlopnevek, adattípusok), mint azt az adatkészletet, amelyhez az átalakítást először tervezték.  
  
4. Más paramétereket nem kell beállítani, mert az átalakítás definiálásakor az összes Testreszabás megtörténik.  
  
5. Ha egy átalakítást szeretne alkalmazni az új adatkészletre, futtassa a folyamatot.  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
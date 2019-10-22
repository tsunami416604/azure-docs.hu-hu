---
title: 'Átalakítás alkalmazása: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan módosíthatja a bemeneti adatkészletet egy korábban számított transzformáció alapján a Azure Machine Learning szolgáltatás átalakítási moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: adb2477fe2b7eea0dd0a98c7240843d90411d832
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693878"
---
# <a name="apply-transformation-module"></a>Átalakítási modul alkalmazása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

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

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
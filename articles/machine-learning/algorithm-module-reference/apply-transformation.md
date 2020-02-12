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
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137580"
---
# <a name="apply-transformation-module"></a>A alkalmazni átalakítási modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal módosíthatja a bemeneti adatkészletet egy korábban számított transzformáció alapján.  
  
Ha például a z-score-t használta a betanítási adatok normalizálása érdekében a **normalizálás** adatmodul használatával, akkor azt a z-score értéket érdemes használni, amelyet a program a pontozási fázisban is kiszámított képzéshez. Azure Machine Learning a normalizálás módszerét átalakíthatja átalakításként, majd az **átalakítás alkalmazása** paranccsal alkalmazhatja a z-pontszámot a bemeneti adatokra a pontozás előtt.
  
Azure Machine Learning támogatást nyújt számos különböző egyéni átalakítás létrehozásához, majd alkalmazásához. Előfordulhat például, hogy menteni szeretné, majd újrahasznosítja az átalakításokat a következőre:  
  
- Hiányzó értékek eltávolítása vagy cseréje a **tiszta hiányzó adatok** használatával
- Adatnormalizálás az **Adatnormalizálás** használatával
  

## <a name="how-to-use-apply-transformation"></a>Az átalakítás alkalmazása  
  
1. Adja hozzá az **átalakítási modul alkalmazása** a folyamathoz lehetőséget. Ez a modul a **modell pontozási & kiértékelési** kategóriában található. 
  
2. Keressen egy meglévő átalakítást bemenetként való használatra. A korábban mentett átalakítások a bal oldali modul fájának **adatkészletek kategóriája** alatt találhatók a **saját adatkészletek** csoportban.  
  
   
  
3. Az átalakítani kívánt adatkészlet csatlakoztatása. Az adatkészletnek pontosan ugyanazzal a sémával kell rendelkeznie (oszlopok száma, oszlopnevek, adattípusok), mint azt az adatkészletet, amelyhez az átalakítást először tervezték.  
  
4. Más paramétereket nem kell beállítani, mert az átalakítás definiálásakor az összes Testreszabás megtörténik.  
  
5. Ha egy átalakítást szeretne alkalmazni az új adatkészletre, futtassa a folyamatot.  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
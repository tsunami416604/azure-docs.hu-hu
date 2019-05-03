---
title: 'Átalakítás vonatkoznak: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a alkalmazni átalakítási modul használata az Azure Machine Learning szolgáltatás egy korábban számított átalakítás alapján bemeneti adatkészlet módosítása.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028710"
---
# <a name="apply-transformation-module"></a>A alkalmazni átalakítási modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával módosítsa a bemeneti adatkészlet egy korábban számított átalakítás alapján.  
  
Például, ha a betanítási adatok optimalizálására a z-pontszámok használta a **normalizálása adatok** modul szeretne számolta z-pontszám érték használata képzéshez a pontozási fázis során. Az Azure Machine Learning, mentheti a normalizálási metódus egy átalakítási és majd használatával **átalakítás alkalmazása** a z-pontszám alkalmazni a bemeneti adatokat a kiértékelés előtt.
  
Az Azure Machine Learning létrehozásához, és ezután alkalmazza az egyéni átalakítások számos különböző típusú támogatást biztosít. Például előfordulhat, hogy szeretné menteni, és újból felhasználhatók az átalakításokat is:  
  
- Távolítsa el vagy cserélje le a hiányzó értékek használatával **Clean Missing Data**
- Adatok optimalizálása **adatok normalizálása**
  

## <a name="how-to-use-apply-transformation"></a>Hogyan használható a alkalmazni átalakítása  
  
1. Adja hozzá a **átalakítás alkalmazása** modult a kísérletvászonra. Ez a modul alatt található **Machine Learning**, a a **pontszám** kategória. 
  
2. Keresse meg egy meglévő átalakítást egy bemeneti adatokként.  A korábban mentett átalakítások található a **alakítja át az** csoportot a bal oldali navigációs ablaktáblán.  
  
   
  
3. Az adatkészlet, amely átalakítja a kívánt kapcsolatot. Az adatkészlet kell pontosan ugyanazzal a sémával rendelkezik (az oszlopok, az oszlopnevek, adattípusok száma), az adatkészletet, amelynek az átalakítás először tervezték.  
  
4. Nincs más paramétereket kell állítani, mivel minden testreszabás történik az átalakítás meghatározásakor.  
  
5. Átalakítás alkalmazni kívánja az új adatkészletre, futtassa a kísérletet.  

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 
---
title: 'Sorok hozzáadása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a sorok hozzáadása a modul az Azure Machine Learning szolgáltatás fűzze össze két adatkészletet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028755"
---
# <a name="add-rows-module"></a>Sorok modul hozzáadása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul segítségével összefűzhet két adatkészletet. A második adatkészlet sorait összefűző, az első adatkészletet végén rendeljük hozzá.  
  
Sorok összefűzésén például a következő esetekben hasznos:  
  
+ Értékelési statisztika sorozatát készítette, és szeretné úgy, hogy egy tábla a könnyebb jelentéskészítés.  
  
+ Különböző adatkészletekkel dolgozik, és az adatkészletek a végső adatkészlet létrehozásához összevonására.  

## <a name="how-to-use-add-rows"></a>Sorok hozzáadása használata  

Összefűzhet két adatkészletet azon sorait, a sorok pontosan ugyanazzal a sémával kell rendelkeznie. Ez azt jelenti, az azonos számú oszlopot, és az azonos típusú adatokat az oszlopok.

1.  Húzza a **sorok hozzáadása** modult is futtathatja a kísérletet, annak a **adatátalakítás**, a a **kezelése** kategória.

2. Csatlakozzon az adatkészletet a két bemeneti port. Az adatkészletet, amely a hozzáfűzni kívánt kapcsolódnia kell a második (jobb oldali) portot. 
  
3.  Futtassa a kísérletet. A kimeneti adatkészlet sorainak száma meg kell egyeznie az mindkét bemeneti adatkészletek a sorok összege.

    Ha az ugyanahhoz az adatkészlethez hozzá mindkét bemenetei a **sorok hozzáadása** modul, az adatkészlet je duplicitní. 

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 
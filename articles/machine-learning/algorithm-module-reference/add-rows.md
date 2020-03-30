---
title: 'Sorok hozzáadása: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Sorok hozzáadása modult az Azure Machine Learningben két adatkészlet összefűzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477731"
---
# <a name="add-rows-module"></a>Sorok hozzáadása modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal két adatkészletet fűzhet össze. Összefűzéskor a második adatkészlet sorai hozzáadódnak az első adatkészlet végéhez.  
  
A sorok összefűzése olyan esetekben hasznos, mint például a következő:  
  
+ Számos értékelési statisztikát hozott létre, és a könnyebb jelentéskészítés érdekében egyetlen táblába szeretné egyesíteni őket.  
  
+ Különböző adatkészletekkel dolgozik, és az adatkészleteket végleges adatkészlet létrehozásához kombinálni szeretné.  

## <a name="how-to-use-add-rows"></a>Sorok hozzáadása  

Két adatkészlet sorainak összefűzéséhez a soroknak pontosan ugyanazzal a sémával kell rendelkezniük. Ez azt jelenti, hogy ugyanannyi oszlop és azonos típusú adat jelenik meg az oszlopokban.

1.  Húzza a **Sorok hozzáadása** modult a folyamatba, Az **Adatátalakítás**területen található.

2. Csatlakoztassa az adatkészleteket a két bemeneti porthoz. A hozzáfűzött adatkészletet a második (jobb oldali) porthoz kell csatlakoztatni. 
  
3.  Küldje el a folyamatot. A kimeneti adatkészlet sorainak számának meg kell egyeznie a két bemeneti adatkészlet sorainak összegével.

    Ha ugyanazt az adatkészletet adja hozzá a **Sorok hozzáadása** modul mindkét bemenetéhez, az adatkészlet duplikálódik. 

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
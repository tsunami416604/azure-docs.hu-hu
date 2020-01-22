---
title: 'Sorok hozzáadása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan összefűzheti a két adathalmazt a Azure Machine Learning sorok hozzáadása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 39bbf3b3a38926d696233c2d77bae83ccfc85206
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314555"
---
# <a name="add-rows-module"></a>Sorok hozzáadása modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal összefűzheti a két adatkészletet. Az Összefűzés során a rendszer hozzáadja a második adatkészlet sorait az első adatkészlet végéhez.  
  
A sorok összefűzése olyan helyzetekben hasznos, mint például a következők:  
  
+ Létrehozta az értékelési statisztikák sorozatát, és egy táblázatba szeretné egyesíteni a könnyebb jelentéskészítés érdekében.  
  
+ Különböző adatkészletekkel dolgozik, és a végső adatkészlet létrehozásához egyesíteni kívánja az adatkészleteket.  

## <a name="how-to-use-add-rows"></a>A sorok hozzáadása  

Két adatkészletből származó sorok összefűzéséhez a soroknak pontosan ugyanazzal a sémával kell rendelkezniük. Ez azt jelenti, hogy ugyanaz a számú oszlop és ugyanolyan típusú adattípus szerepel az oszlopokban.

1.  Húzza a **sorok hozzáadása** modult a folyamatba, és az **adatátalakítás**alatt található a **manipulálás** kategóriában.

2. Az adatkészletek csatlakoztatása a két bemeneti porthoz. A hozzáfűzni kívánt adatkészletet a második (jobb oldali) porthoz kell csatlakoztatni. 
  
3.  A folyamat futtatása. A kimeneti adatkészlet sorainak számának egyenlőnek kell lennie a bemeneti adatkészletek sorainak összegével.

    Ha ugyanazt az adatkészletet adja hozzá a **sorok hozzáadása** modul mindkét bemenetéhez, a rendszer duplikálja az adatkészletet. 

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
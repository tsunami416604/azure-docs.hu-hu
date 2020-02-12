---
title: 'Sorok hozzáadása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan összefűzheti a két adathalmazt a Azure Machine Learning sorok hozzáadása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 71f15d959bf9d42e67cd7c35ca91d6cd2caa718d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152465"
---
# <a name="add-rows-module"></a>Sorok hozzáadása modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

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
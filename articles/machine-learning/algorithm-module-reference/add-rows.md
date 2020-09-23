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
ms.date: 02/22/2020
ms.openlocfilehash: 353ecc89e242e1d17dd83436f83afe77cc8c84ae
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898978"
---
# <a name="add-rows-module"></a>Sorok hozzáadása modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal összefűzheti a két adatkészletet. Az Összefűzés során a rendszer hozzáadja a második adatkészlet sorait az első adatkészlet végéhez.  
  
A sorok összefűzése olyan helyzetekben hasznos, mint például a következők:  
  
+ Létrehozta az értékelési statisztikák sorozatát, és egy táblázatba szeretné egyesíteni a könnyebb jelentéskészítés érdekében.  
  
+ Különböző adatkészletekkel dolgozik, és a végső adatkészlet létrehozásához egyesíteni kívánja az adatkészleteket.  

## <a name="how-to-use-add-rows"></a>A sorok hozzáadása  

Két adatkészletből származó sorok összefűzéséhez a soroknak pontosan ugyanazzal a sémával kell rendelkezniük. Ez azt jelenti, hogy ugyanaz a számú oszlop és ugyanolyan típusú adattípus szerepel az oszlopokban.

1.  Húzza a **sorok hozzáadása** modult a folyamatba, és az **adatátalakítás**alatt találja.

2. Az adatkészletek csatlakoztatása a két bemeneti porthoz. A hozzáfűzni kívánt adatkészletet a második (jobb oldali) porthoz kell csatlakoztatni. 
  
3.  A folyamat elküldése. A kimeneti adatkészlet sorainak számának egyenlőnek kell lennie a bemeneti adatkészletek sorainak összegével.

    Ha ugyanazt az adatkészletet adja hozzá a **sorok hozzáadása** modul mindkét bemenetéhez, a rendszer duplikálja az adatkészletet. 

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
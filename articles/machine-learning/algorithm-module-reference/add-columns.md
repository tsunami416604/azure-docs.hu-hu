---
title: 'Oszlopok hozzáadása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan összefűzheti a két adathalmazt a Azure Machine Learning oszlopok hozzáadása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 55981279cb1902424d1a0f77af097dc379d7222f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493973"
---
# <a name="add-columns-module"></a>Oszlopok hozzáadása modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal összefűzheti a két adatkészletet. Egyetlen adatkészlet létrehozásához a bemenetként megadott két adatkészlet összes oszlopát össze kell kapcsolni. Ha kettőnél több adatkészletet kell összefűzni, használja az **Oszlopok hozzáadása**több példányát.



## <a name="how-to-configure-add-columns"></a>Az oszlopok hozzáadásának beállítása
1. Adja hozzá az **Oszlopok hozzáadása** modult a folyamathoz.

2. Kösse össze az összefűzni kívánt két adatkészletet. Ha kettőnél több adatkészletet szeretne egyesíteni, összekapcsolhatja az **Oszlopok hozzáadása**több kombinációját is.

    - Két olyan oszlopot is össze lehet kapcsolni, amelyek eltérő számú sorral rendelkeznek. A kimeneti adatkészlet a kisebb forrás oszlop minden sorának hiányzó értékeivel van feltöltve.

    - Nem választhat egyes oszlopokat a hozzáadáshoz. Az egyes adatkészletek összes oszlopa összefűzve lesz az **oszlopok hozzáadásakor**. Ezért ha csak az oszlopok egy részhalmazát szeretné felvenni, az adatkészletben az Oszlopok kiválasztása lehetőséggel hozzon létre egy adatkészletet, amely a kívánt oszlopokat használja.

3. A folyamat futtatása.

### <a name="results"></a>Results (Eredmények)
A folyamat futtatása után:

- Az új adatkészlet első sorainak megjelenítéséhez kattintson a jobb gombbal az **Oszlopok hozzáadása** elemre, majd válassza a Megjelenítés lehetőséget.

Az új adatkészlet oszlopainak száma megegyezik a bemeneti adatkészletek oszlopainak összegével.

Ha két azonos nevű oszlop szerepel a bemeneti adatkészletekben, a rendszer egy numerikus utótagot ad hozzá az oszlop nevéhez. Ha például egy TargetOutcome nevű oszlop két példánya van, a bal oldali oszlop átnevezve lesz TargetOutcome_1, és a jobb oldali oszlop átnevezve lesz TargetOutcome_2.

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
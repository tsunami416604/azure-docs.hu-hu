---
title: 'Oszlopok hozzáadása: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan összefűzheti a két adathalmazt a Azure Machine Learning-szolgáltatás oszlopok hozzáadása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: cbb1249b529fde1368bfaff0c6c251c93fa6c309
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693863"
---
# <a name="add-columns-module"></a>Oszlopok hozzáadása modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal összefűzheti a két adatkészletet. Egyetlen adatkészlet létrehozásához a bemenetként megadott két adatkészlet összes oszlopát össze kell kapcsolni. Ha kettőnél több adatkészletet kell összefűzni, használja az **Oszlopok hozzáadása**több példányát.



## <a name="how-to-configure-add-columns"></a>Az oszlopok hozzáadásának beállítása
1. Adja hozzá az **Oszlopok hozzáadása** modult a folyamathoz.

2. Kösse össze az összefűzni kívánt két adatkészletet. Ha kettőnél több adatkészletet szeretne egyesíteni, összekapcsolhatja az **Oszlopok hozzáadása**több kombinációját is.

    - Két olyan oszlopot is össze lehet kapcsolni, amelyek eltérő számú sorral rendelkeznek. A kimeneti adatkészlet a kisebb forrás oszlop minden sorának hiányzó értékeivel van feltöltve.

    - Nem választhat egyes oszlopokat a hozzáadáshoz. Az egyes adatkészletek összes oszlopa összefűzve lesz az **oszlopok hozzáadásakor**. Ezért ha csak az oszlopok egy részhalmazát szeretné felvenni, az adatkészletben az Oszlopok kiválasztása lehetőséggel hozzon létre egy adatkészletet, amely a kívánt oszlopokat használja.

3. A folyamat futtatása.

### <a name="results"></a>Eredmények
A folyamat futtatása után:

- Az új adatkészlet első sorainak megjelenítéséhez kattintson a jobb gombbal az **Oszlopok hozzáadása** elemre, majd válassza a Megjelenítés lehetőséget.

Az új adatkészlet oszlopainak száma megegyezik a bemeneti adatkészletek oszlopainak összegével.

Ha két azonos nevű oszlop szerepel a bemeneti adatkészletekben, a rendszer egy numerikus utótagot ad hozzá az oszlop nevéhez. Ha például egy TargetOutcome nevű oszlop két példánya van, a bal oldali oszlop átnevezve lesz TargetOutcome_1, és a jobb oldali oszlop átnevezve lesz TargetOutcome_2.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
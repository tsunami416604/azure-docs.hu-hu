---
title: 'Oszlopok hozzáadása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan összefűzheti a két adathalmazt a Azure Machine Learning oszlopok hozzáadása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79456778"
---
# <a name="add-columns-module"></a>Oszlopok hozzáadása modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal összefűzheti a két adatkészletet. Egyetlen adatkészlet létrehozásához a bemenetként megadott két adatkészlet összes oszlopát össze kell kapcsolni. Ha kettőnél több adatkészletet kell összefűzni, használja az **Oszlopok hozzáadása**több példányát.



## <a name="how-to-configure-add-columns"></a>Az oszlopok hozzáadásának beállítása
1. Adja hozzá az **Oszlopok hozzáadása** modult a folyamathoz.

2. Kösse össze az összefűzni kívánt két adatkészletet. Ha kettőnél több adatkészletet szeretne egyesíteni, összekapcsolhatja az **Oszlopok hozzáadása**több kombinációját is.

    - Két olyan oszlopot is össze lehet kapcsolni, amelyek eltérő számú sorral rendelkeznek. A kimeneti adatkészlet a kisebb forrás oszlop minden sorának hiányzó értékeivel van feltöltve.

    - Nem választhat egyes oszlopokat a hozzáadáshoz. Az egyes adatkészletek összes oszlopa összefűzve lesz az **oszlopok hozzáadásakor**. Ezért ha csak az oszlopok egy részhalmazát szeretné felvenni, az adatkészletben az Oszlopok kiválasztása lehetőséggel hozzon létre egy adatkészletet, amely a kívánt oszlopokat használja.

3. A folyamat elküldése.

### <a name="results"></a>Results (Eredmények)
A folyamat futtatása után:

- Az új adatkészlet első sorainak megtekintéséhez kattintson a jobb gombbal az **Oszlopok hozzáadása** modulra, és válassza a Megjelenítés lehetőséget. Vagy válassza ki a modult, és váltson a **kimenetek** lapra a jobb oldali panelen, kattintson a **port kimenetének** hisztogram ikonjára az eredmény megjelenítéséhez.

Az új adatkészlet oszlopainak száma megegyezik a bemeneti adatkészletek oszlopainak összegével.

Ha két azonos nevű oszlop szerepel a bemeneti adatkészletekben, a rendszer egy numerikus utótagot ad hozzá az oszlop nevéhez. Ha például egy TargetOutcome nevű oszlop két példánya van, a bal oldali oszlop átnevezve lesz TargetOutcome_1, és a jobb oldali oszlop átnevezve lesz TargetOutcome_2.

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
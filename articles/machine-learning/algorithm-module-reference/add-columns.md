---
title: 'Oszlopok hozzáadása: Modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Oszlopok hozzáadása modult az Azure Machine Learningben két adatkészlet összefűzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456778"
---
# <a name="add-columns-module"></a>Oszlopok hozzáadása modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal két adatkészletet fűzhet össze. A bemenetként megadott két adatkészlet összes oszlopát kombinálva egyetlen adatkészletet hozhat létre. Ha kettőnél több adatkészletet kell összefűznie, használja az Oszlopok hozzáadása több **példányát.**



## <a name="how-to-configure-add-columns"></a>Az Oszlopok hozzáadása beállítása
1. Adja hozzá az **Oszlopok hozzáadása** modult a folyamathoz.

2. Csatlakoztassa az összefonni kívánt két adatkészletet. Ha kettőnél több adatkészletet szeretne egyesíteni, az Oszlopok **hozzáadása**több kombinációját is összekötheti.

    - Két olyan oszlop kombinálható, amelyek különböző számú sorral rendelkeznek. A kimeneti adatkészlet a kisebb forrásoszlop minden egyes sorának hiányzó értékeivel van kitömve.

    - Nem választhatja ki a hozzáadni kívánt oszlopokat. **Az oszlopok hozzáadása**használatakor az egyes adatkészletek összes oszlopa összelesz fűzve. Ezért ha az oszlopoknak csak egy részét szeretné hozzáadni, az Adatkészlet Oszlopkijelölése segítségével hozzon létre adatkészletet a kívánt oszlopokkal.

3. Küldje el a folyamatot.

### <a name="results"></a>Results (Eredmények)
A folyamat futtatása után:

- Az új adatkészlet első sorainak megtekintéséhez kattintson a jobb gombbal az **Oszlopok hozzáadása** modulra, és válassza a Megjelenítés parancsot. Vagy Válassza ki a **modult, és váltson** a jobb oldali panel Kimenetek fülére, kattintson a hisztogram ikonra a **Port kimenetekben** az eredmény megjelenítéséhez.

Az új adatkészlet oszlopainak száma megegyezik a két bemeneti adatkészlet oszlopainak összegével.

Ha a bemeneti adatkészletek két azonos nevű oszlopot tartalmaznak, a numerikus utótag hozzáadódik az oszlop nevéhez. Ha például a TargetOutcome nevű oszlopnak két példánya van, a bal oldali oszlop ot TargetOutcome_1, a jobb oldali oszlopot pedig TargetOutcome_2.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
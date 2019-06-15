---
title: 'Oszlopok hozzáadása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja az oszlopok hozzáadása a modul az Azure Machine Learning szolgáltatás fűzze össze két adatkészletet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029355"
---
# <a name="add-columns-module"></a>Oszlopok modul hozzáadása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul segítségével összefűzhet két adatkészletet. Egyetlen adatkészlet létrehozása a bemenetként megadott a két adatkészlet összes oszlopa kombinálja. Ha fűzze össze a több mint két adatkészletet kell, akkor több példánya **oszlopok hozzáadása**.



## <a name="how-to-configure-add-columns"></a>Oszlopok hozzáadása konfigurálása
1. Hozzáadás a **oszlopok hozzáadása** modult a kísérletvászonra.

2. A két adatkészletet, amely fűzze össze szeretné csatlakoztatni. Ha azt szeretné, úgy, hogy több mint két adatkészletet, láncolhatja össze több kombinációit **oszlopok hozzáadása**.

    - Úgy, hogy két oszlopot, amelyek eltérő mennyiségű sor lehetőség. A kimeneti adatkészlet számokhoz a kisebb forrásoszlop minden egyes sorára a hiányzó értékeket.

    - Az egyes oszlopok hozzáadása nem választható. Minden adathalmaz összes oszlopát vannak a használatakor összefűzött **oszlopok hozzáadása**. Ezért adja hozzá az oszlopok csak egy részhalmazát szeretné, ha használatával Select Columns adatkészlet adatkészlet létrehozása a kívánt oszlopokat.

3. Futtassa a kísérletet.

### <a name="results"></a>Results (Eredmények)
A kísérlet után futott:

- Az első sort az új adatkészlet megtekintéséhez kattintson a jobb gombbal a kimenetét **oszlopok hozzáadása** , és válassza ki a képi megjelenítés opcióra.

Az új adatkészlet oszlopok száma egyenlő mindkét bemeneti adatkészletek az oszlopok összege.

Ha két oszlop a bemeneti adatkészletek ugyanazzal a névvel, egy numerikus utótagot ad hozzá az oszlop neve. Például ha két példánya TargetOutcome nevű oszlop, a bal oldali oszlopban lenne nevezhető TargetOutcome_1 és a jobb oldali oszlopban lenne TargetOutcome_2 átnevezték.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 
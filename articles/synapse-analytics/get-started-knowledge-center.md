---
title: 'Oktatóanyag: első lépések a szinapszis Tudásközpont megismeréséhez'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a szinapszis tudásbázist.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 47b8c45e22569cc758d00fb8534f409ecebf58ca
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299931"
---
# <a name="explore-the-synapse-knowledge-center"></a>A szinapszis Tudásközpont megismerése

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a szinapszis Studio Knowledge centert.

## <a name="getting-to-the-knowledge-center"></a>Ismerkedés a Knowledge centerrel

A Knowledge Center a szinapszis Studióban kétféleképpen megtalálható:

  1. A Kezdőlap központ hasznos hivatkozások területén kattintson a **Knowledge Center**nevű első hivatkozásra.
  2. A felső menüsorban kattintson a gombra **?** és a  **Knowledge Center**.

Válassza a metódust, és nyissa meg a **tudásbázist**.

## <a name="overview"></a>Áttekintés

A **Tudásközpont** három dolgot tesz lehetővé:
* A **mintákat azonnal használhatja**. Ez a beállítás úgy van optimalizálva, hogy a lehető leggyorsabban lássa az elemzést. Ha a szinapszis működésének gyors példáját szeretné használni, válassza ezt a lehetőséget.
* A **böngésző elérhető mintája**. Ez a beállítás lehetővé teszi a mintaadatok-készletek összekapcsolását és a mintakód hozzáadását SQL-parancsfájlok, jegyzetfüzetek és folyamatok formájában.
* A **szinapszis Studio**bemutatása. Ez a lehetőség a szinapszis Studio alaprészének rövid bemutatóját veszi igénybe. Ez akkor hasznos, ha még soha nem használta a szinapszis Studio alkalmazást.

## <a name="exploring-blob-storage-with-sql-on-demand"></a>A blob Storage és az SQL igény szerinti felderítése

1. A **Tudásbázisban**kattintson a **minták azonnali használata** elemre.
1. **SQL-alapú lekérdezések** kiválasztása 
1. Kattintson a **minták azonnali használata** elemre.
1. Ekkor létrejön egy új SQL-szkript.
1. Görgessen az első lekérdezéshez (28 – 32. sor), és válassza ki a lekérdezés szövegét.
1. Kattintson a Futtatás elemre. A kiválasztott szöveget fogja futtatni.

## <a name="loading-more-nyc-taxi-data"></a>A New York-i taxi-adathalmazok betöltése
1. A **Tudásbázisban**kattintson az **elérhető minták tallózása** elemre. 
1. Válassza a felül található **SQL-parancsfájlok** lapot.
1. Válassza **a New York taxik-adatkészlet betöltése** lehetőséget.
1. A **bemenetek**területen válassza **a meglévő készlet kijelölése** lehetőséget, és válassza a **SQLDB1** lehetőséget.
1. Kattintson a **parancsfájl megnyitása** lehetőségre.
1. Ekkor megjelenik egy új SQL-szkript.
1. Kattintson a **Futtatás** gombra.
1. Ez több táblát hoz létre a New York-i taxik adataihoz, és betölti azokat a T-SQL COPY paranccsal.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés az Azure szinapszis Analytics szolgáltatással](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)

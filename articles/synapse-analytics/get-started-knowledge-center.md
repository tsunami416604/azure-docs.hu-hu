---
title: 'Oktatóanyag: első lépések a szinapszis Tudásközpont megismeréséhez'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a szinapszis tudásbázist.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 461fabd0dd9948e8967ac61919f77e3e23a981b9
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331957"
---
# <a name="explore-the-synapse-knowledge-center"></a>A szinapszis Tudásközpont megismerése

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a szinapszis Studio Knowledge centert.

## <a name="getting-to-the-knowledge-center"></a>Ismerkedés a Knowledge centerrel

A Knowledge Center a szinapszis Studióban kétféleképpen megtalálható:

  1. A Kezdőlap központ hasznos hivatkozások területén kattintson a **Knowledge Center** nevű első hivatkozásra.
  2. A felső menüsorban kattintson a gombra **?** és a  **Knowledge Center**.

Válassza a metódust, és nyissa meg a **tudásbázist**.

## <a name="overview"></a>Áttekintés

A **Tudásközpont** három dolgot tesz lehetővé:
* A **mintákat azonnal használhatja**. Ez a beállítás úgy van optimalizálva, hogy a lehető leggyorsabban lássa az elemzést. Ha a szinapszis működésének gyors példáját szeretné használni, válassza ezt a lehetőséget.
* A **böngésző elérhető mintája**. Ez a beállítás lehetővé teszi a mintaadatok-készletek összekapcsolását és a mintakód hozzáadását SQL-parancsfájlok, jegyzetfüzetek és folyamatok formájában.
* A **szinapszis Studio** bemutatása. Ez a lehetőség a szinapszis Studio alaprészének rövid bemutatóját veszi igénybe. Ez akkor hasznos, ha még soha nem használta a szinapszis Studio alkalmazást.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>BLOB Storage felderítése kiszolgáló nélküli SQL-készlettel

1. Nyissa meg a **Knowledge centert** , kattintson a **minták azonnali használata** elemre.
1. **SQL-alapú lekérdezések** kiválasztása 
1. Kattintson a **minták azonnali használata** elemre.
1. Ekkor létrejön egy új SQL-szkript.
1. Görgessen az első lekérdezéshez (28 – 32. sor), és válassza ki a lekérdezés szövegét.
1. Kattintson a Futtatás elemre. A kiválasztott szöveget fogja futtatni.

## <a name="loading-more-nyc-taxi-data"></a>A New York-i taxi-adathalmazok betöltése
1. Nyissa meg a **Knowledge centert** , kattintson az **elérhető minták tallózása** lehetőségre. 
1. Válassza a felül található **SQL-parancsfájlok** lapot.
1. Válassza **a New York taxik-adatkészlet betöltése** lehetőséget.
1. A **bemenetek** területen válassza **a meglévő készlet kijelölése** lehetőséget, és válassza a **SQLDB1** lehetőséget.
1. Kattintson a **parancsfájl megnyitása** lehetőségre.
1. Ekkor megjelenik egy új SQL-szkript.
1. Kattintson a **Futtatás** gombra.
1. Ez több táblát hoz létre a New York-i taxik adataihoz, és betölti azokat a T-SQL COPY paranccsal.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure szinapszis Analytics szolgáltatással](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)

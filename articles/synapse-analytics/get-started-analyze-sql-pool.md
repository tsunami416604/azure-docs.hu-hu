---
title: 'Oktatóanyag: az SQL-készlettel történő adatelemzés első lépései'
description: Ebben az oktatóanyagban a New York-i taxi-mintaadatok segítségével megismerheti az SQL-készlet analitikus funkcióit.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 948de6968d336d16554fe0a0c2f7d0c5b7367f15
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101628"
---
# <a name="analyze-data-with-sql-pools"></a>Az SQL-készletekkel rendelkező adatelemzés

Az Azure szinapszis Analytics lehetővé teszi az SQL-készlettel való adatelemzés lehetőségét. Ebben az oktatóanyagban a New York-i taxi-mintaadatok segítségével megismerheti az SQL-készlet analitikus funkcióit.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>A New York-i taxi-mintaadatok betöltése a SQLDB1-adatbázisba

1. A szinapszis Studióban a legfelső kék menüben válassza a kérdőjel (**?**) ikont.
1. Válassza az **első**lépéseket az első  >  **lépések központban**.
1. A **lekérdezési mintaadatok**feliratú kártyán válassza ki a **SQLDB1**nevű SQL-készletet.
1. Válassza ki a **lekérdezési adatelemet**. A "mintaadatok betöltése" értesítés röviden megjelenik. A szinapszis Studio teteje közelében lévő világoskék állapotsor azt jelzi, hogy a rendszer az SQLDB1-be tölti be az adatmennyiséget.
1. Az állapotsor zöldre váltása után zárja be a következőt:.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Ismerkedjen meg az SQL-készletben található New York-i taxi-szolgáltatással

1. A szinapszis Studióban nyissa meg **az** adatközpontot.
1. Nyissa meg a **SQLDB1**  >  **táblákat**. Ekkor több táblázat is betöltődik.
1. Kattintson a jobb gombbal a **dbo. Utazási** táblázat és válassza az **új SQL-parancsfájl**lehetőséget, majd  >  **válassza a Top 100 sort**.
1. Várjon, amíg a rendszer létrehoz egy új SQL-parancsfájlt, és futtatja azt.
1. Figyelje meg, hogy az SQL-parancsfájl felső részén a **Kapcsolódás** automatikusan a **SQLDB1**nevű SQL-készletre lesz beállítva.
1. Cserélje le az SQL-parancsfájl szövegét ezzel a kóddal, majd futtassa.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Ez a lekérdezés azt mutatja be, hogy a teljes utazási távolság és az átlagos utazási távolság az utasok számával függ.
1. Az SQL-parancsfájl eredményének ablakában módosítsa **View** a nézetet **diagramra** , hogy megjelenítse az eredmények vonal diagramként való megjelenítését.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Elemzés a Spark használatával](get-started-analyze-spark.md)


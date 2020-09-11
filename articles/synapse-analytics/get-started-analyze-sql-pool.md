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
ms.openlocfilehash: 363f2934bbeec266c16711572620e03e69785f94
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007196"
---
# <a name="analyze-data-with-sql-pools"></a>Az SQL-készletekkel rendelkező adatelemzés

Az Azure szinapszis Analytics lehetővé teszi az SQL-készlettel való adatelemzés lehetőségét. Ebben az oktatóanyagban a New York-i taxi-mintaadatok segítségével megismerheti az SQL-készlet analitikus funkcióit.

## <a name="link-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>A New York-i taxi-mintaadatok összekapcsolása a SQLDB1-adatbázissal

1. A szinapszis Studióban navigáljon a **bal oldali adatközponthoz** .
1. Kattintson **+** a, majd a **Tallózás minták**elemre. Ekkor megnyílik a **minta központ** , és megnyílik az **adatkészletek** lap.
1. Válassza a **New York-i taxi & Limousine Commission – Yellow taxi Trip Records**elemet. Ez az adatkészlet több mint 1 500 000 000 sort tartalmaz.
1. Kattintson az **adatkészlet hozzáadása** elemre.
1. Az **adatközpontban** a **kapcsolt** területen egy új adatkészlet jelenik meg az **Azure Blob Storage > minta adatkészletek > nyc_tlc_yellow**   
1. A **lekérdezési mintaadatok**feliratú kártyán válassza ki a **SQLDB1**nevű SQL-készletet.


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



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés a Spark használatával](get-started-analyze-spark.md)


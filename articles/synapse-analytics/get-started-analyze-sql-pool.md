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
ms.openlocfilehash: b1060bcc8603cb7f7395a50056424b3d6c0ebe5a
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015500"
---
# <a name="analyze-data-with-sql-pools"></a>Az SQL-készletekkel rendelkező adatelemzés

Az Azure szinapszis Analytics lehetővé teszi az SQL-készlettel való adatelemzés lehetőségét. Ebben az oktatóanyagban a New York-i taxi-mintaadatok segítségével megismerheti az SQL-készlet analitikus funkcióit.

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>A New York-i taxi-szolgáltatás betöltése a SQLDB1

1. A szinapszis Studióban navigáljon a **fejlesztés** hubhoz, majd hozzon létre új SQL-parancsfájlt.
1. Írja be a következő kódot:
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. A szkript futtatása körülbelül 1 percet vesz igénybe. Egy dbo nevű táblába tölti be a New York-i, 2 000 000-as sorokat **. Utazás**

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


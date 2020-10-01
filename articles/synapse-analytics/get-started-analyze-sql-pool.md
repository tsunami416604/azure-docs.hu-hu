---
title: 'Oktatóanyag: az adatelemzés első lépései dedikált SQL-készletekkel'
description: Ebben az oktatóanyagban a New York-i taxi-mintaadatok segítségével megismerheti az SQL-készlet analitikus funkcióit.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 11c11d3e43f15b569a74c282b15a95525cea7e7c
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620035"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Az adatelemzés dedikált SQL-készletekkel

Az Azure szinapszis Analytics lehetővé teszi az adatelemzést egy dedikált SQL-készlettel. Ebben az oktatóanyagban a New York-i taxi-adatszolgáltatások segítségével megismerheti a dedicted SQL-készlet képességeit.

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

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>A New York-i taxi adatai a dedikált SQL-készletben

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


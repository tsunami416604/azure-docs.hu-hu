---
title: 'Oktatóanyag: az adatelemzés megkezdése kiszolgáló nélküli SQL-készlettel'
description: Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az információkat egy kiszolgáló nélküli SQL-készlettel a Spark-adatbázisokban található adatforrások használatával.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209406"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Az Azure szinapszis Analytics szolgáltatásban kiszolgáló nélküli SQL-készlettel elemezheti az adatelemzést

Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az információkat a kiszolgáló nélküli SQL-készlettel a Spark-adatbázisokban található adatforrások használatával. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>NYC-alapú taxi-adatelemzés a blob Storage-ban kiszolgáló nélküli SQL-készlettel

1. Az **adatközpontban** kattintson a jobb gombbal az **Azure Blob Storage > minta adatkészletek > NYC_TLC_YELLOW**, **új SQL-parancsfájl** elemre **, és** válassza a **legfelső 100-sorok kiválasztása** lehetőséget.
1. Ekkor létrejön egy új SQL-parancsfájl a következő kóddal:

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Kattintson a **Futtatás** gombra.

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>New York-i taxi-adatelemzés a Spark-adatbázisokban kiszolgáló nélküli SQL-készlettel

A Spark-adatbázisok táblái automatikusan láthatók, és a kiszolgáló nélküli SQL-készlet által is lekérdezhető.

1. A szinapszis Studióban nyissa meg a **fejlesztés** csomópontot, és hozzon létre egy új SQL-szkriptet.
1. A **Kapcsolódás** a beépített kiszolgáló **nélküli SQL-** készlethez beállítás megadása.
1. Illessze be a következő szöveget a parancsfájlba, és futtassa a parancsfájlt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Amikor a kiszolgáló nélküli SQL-készletet használó lekérdezést először futtatja, a kiszolgáló nélküli SQL-készlet körülbelül 10 másodpercig tart, hogy összegyűjtse a lekérdezések futtatásához szükséges SQL-erőforrásokat. A további lekérdezések sokkal gyorsabban fognak megjelenni.
  


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatelemzés a Storage szolgáltatásban](get-started-analyze-storage.md)

---
title: 'Oktatóanyag: az adatelemzés első lépései a serverles SQL-sel'
description: Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az SQL-on keresztül igénybe venni kívánt információkat a Spark-adatbázisokban található adatforrások használatával.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 8d26a03a8b61850dc17bc4efff5f8ca12dfca191
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300224"
---
# <a name="analyze-data-with-sql-on-demand"></a>Az SQL igény szerinti elemzése

Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az adatforrást a kiszolgáló nélküli SQL használatával egy igény szerinti SQL-készlettel a Spark-adatbázisokban található adatforrások használatával. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>NYC-alapú taxi-adatelemzés a blob Storage-ban SQL igény szerinti készlet használatával

1. Az **adatközpontban** kattintson a jobb gombbal az **Azure Blob Storage > minta adatkészletek > nyc_tlc_yellow** elemre **, és**válassza a **legfelső 100-sorok kiválasztása** lehetőséget.
1. Ekkor létrejön egy új SQL-parancsfájl a következő kóddal:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Kattintson a **Futtatás** gombra.

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>NYC-alapú taxi-adatelemzés a Spark-adatbázisokban az SQL igény szerinti használatával

A Spark-adatbázisok táblái automatikusan láthatók, és az SQL igény szerint is lekérdezhető.

1. A szinapszis Studióban nyissa meg a **fejlesztés** csomópontot, és hozzon létre egy új SQL-szkriptet.
1. Állítsa be a **Kapcsolódás** az **SQL-hez igény szerint**lehetőséget.
1. Illessze be a következő szöveget a parancsfájlba, és futtassa a parancsfájlt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Amikor először futtat egy SQL on-demand lekérdezést, a lekérdezés futtatásához szükséges SQL-erőforrások összegyűjtéséhez körülbelül 10 másodpercre van szükség az SQL igény szerint. A további lekérdezések sokkal gyorsabban fognak megjelenni.
  


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés a Spark használatával](get-started-analyze-spark.md)

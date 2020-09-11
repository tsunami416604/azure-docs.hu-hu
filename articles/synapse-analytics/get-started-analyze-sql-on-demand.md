---
title: 'Oktatóanyag: az SQL igény szerinti elemzésének első lépései'
description: Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az SQL-on keresztül igénybe venni kívánt információkat a Spark-adatbázisokban található adatforrások használatával.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 93ebc1c5e89e54f4813f270b9f8b7b13f672fbe3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016112"
---
# <a name="analyze-data-with-sql-on-demand"></a>Az SQL igény szerinti elemzése

Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az SQL-on keresztül igénybe venni kívánt információkat a Spark-adatbázisokban található adatforrások használatával. 

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-sql-on-demand"></a>NYC-alapú taxi-adattárolás elemzése a blob Storage-ban az SQL igény szerinti használatával

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

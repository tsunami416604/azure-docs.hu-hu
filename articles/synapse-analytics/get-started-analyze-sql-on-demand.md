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
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101548"
---
# <a name="analyze-data-with-sql-on-demand"></a>Az SQL igény szerinti elemzése

Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az SQL-on keresztül igénybe venni kívánt információkat a Spark-adatbázisokban található adatforrások használatával. 

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
  


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Elemzés a Spark használatával](get-started-analyze-spark.md)

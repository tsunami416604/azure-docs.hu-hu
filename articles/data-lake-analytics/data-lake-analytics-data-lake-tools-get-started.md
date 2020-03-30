---
title: Lekérdezés Azure Data Lake Analytics - Visual Studio
description: Ismerje meg, hogyan telepítheti a Data Lake Tools for Visual Studio eszközt, valamint hogyan fejleszthet és tesztelhet U-SQL-parancsfájlokat.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260344"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Az Azure Data Lake és a Stream Analytics tools két Azure-szolgáltatáshoz, az Azure Data Lake Analyticshez és az Azure Stream Analytics szolgáltatáshoz kapcsolódó funkciókat tartalmaz. Az Azure Stream Analytics-forgatókönyvekről az [Azure Stream Analytics-eszközök a Visual Studio számára](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md)című témakörben talál további információt.

Ez a cikk azt ismerteti, hogyan hozhat létre a Visual Studio az Azure Data Lake Analytics-fiókok létrehozásához. Az [U-SQL alkalmazásban](data-lake-analytics-u-sql-get-started.md)definiálhat feladatokat, és elküldheti a feladatokat a Data Lake Analytics szolgáltatásnak. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

> [!IMPORTANT]
> Javasoljuk, hogy frissítsen az Azure Data Lake Tools for Visual Studio 2.3.3000.4-es vagy újabb verziójára. A korábbi verziók már nem tölthetők le, és mostantól elavultnak számítanak.
>
> 1. Ellenőrizze, hogy az Azure Data Lake Tools for Visual Studio 2.3.3000.4-esnél korábbi verzióját használja-e.
>
>    ![Az eszköz verziószámának ellenőrzése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Amennyiben egy, a 2.3.3000.4-esnél korábbi verziót használ, az Azure Data Lake Tools for Visual Studiót a letöltőközpont felkeresésével frissítheti:
>    - [Visual Studio 2017-hez és 2019-hez](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 és 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio**: Az Express kivételével minden kiadás támogatott.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK for .NET** 2.7.1-es vagy újabb verzió. Telepítse a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx).
* **Egy Data Lake Analytics-fiók.** Fiók létrehozásához lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Az Azure Data Lake Tools for Visual Studio telepítése

Ehhez az oktatóanyaghoz feltétel, hogy telepítve legyen a Data Lake Tools for Visual Studio. További információt a Data Lake Tools for Visual Studio telepítése című [témakörben talál.](data-lake-analytics-data-lake-tools-install.md)

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Csatlakozás Azure Data Lake Data Lake Analytics-fiókhoz

1. Nyissa meg a Visual Studiót.

1. A **Kiszolgálókezelő** megnyitásához válassza a**Kiszolgálókezelő** **megtekintése** > lehetőséget.

1. Kattintson a jobb gombbal az **Azure**elemre, majd válassza **a Csatlakozás a Microsoft Azure-előfizetéshez**lehetőséget. A **Bejelentkezés a fiókjába**, kövesse az utasításokat.

1. A **Kiszolgálókezelőben**válassza az **Azure** > **Data Lake Analytics**lehetőséget. Ekkor megjelenik a Data Lake Analytics-fiókok listája.

## <a name="write-your-first-u-sql-script"></a>Az első U-SQL parancsfájl megírása

A következő szöveg egy igen egyszerű U-SQL parancsfájl. Definiál egy kisebb adatkészletet, és az alapértelmezett Data Lake Store-ba írja az adatkészletet, amely a `/data.csv` nevű fájl.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Data Lake Analytics-feladat küldése

1. A Visual Studióban válassza az**Új** > **projekt** **fájlja** > lehetőséget.

1. Jelölje ki az **U-SQL-projekt** típust, majd kattintson a **Tovább**gombra. Az **Új projekt konfigurálása**csoportban válassza a **Létrehozás lehetőséget.**

   A Visual Studio olyan megoldást hoz létre, amely **Script.usql** fájlt tartalmaz.

1. Illessze be a parancsfájlt [az Első U-SQL parancsfájl beírása](#write-your-first-u-sql-script) a **Script.usql** ablakba.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Script.usql**fájlra, és válassza **a Parancsfájl küldése parancsot.**

1. A **Küldési feladat csoportban**válassza ki a Data Lake Analytics-fiókját, és válassza a **Küldés**lehetőséget.

   ![U-SQL Visual Studio-projekt elküldése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

A feladat beküldése után megnyílik a **Feladat nézet** lap, amelyen megtekinthető a feladat előrehaladása.

* A **Feladat összegzése** a feladat összegzését jeleníti meg.
* A **Feladatgrafikon** a feladat előrehaladását jeleníti meg képi formában.
* A **Metaadat-műveletek** bemutatja a U-SQL-katalóguson végrehajtott összes műveletet.
* Az **Adatok** az összes bemenetet és kimenetet jeleníti meg.
* Az **Állapotelőzmények** az idővonallal és az állapottal kapcsolatos adatokat jeleníti meg.
* **Az AU elemzése** azt mutatja, hogy hány AU-t használtak fel a feladatban, és feltárja a különböző AU allokációs stratégiák szimulációit.
* A **Diagnosztika** részletes elemzést nyújt a feladat végrehajtásához és a teljesítmény optimalizálásához.

![U-SQL Visual Studio Data Lake Analytics-feladat teljesítménygrafikonja](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

A feladat legutóbbi állapotának megtekintéséhez és a képernyő frissítéséhez válassza a **Frissítés**lehetőséget.

## <a name="check-job-status"></a>Feladat állapotának ellenőrzése

1. A **Kiszolgálókezelőben**válassza az **Azure** > **Data Lake Analytics**lehetőséget.

1. Bontsa ki a Data Lake Analytics-fiók nevét.

1. Kattintson duplán a **Feladatok** elemre.

1. Válassza ki a korábban elküldött feladatot.

## <a name="see-the-job-output"></a>Feladat kimenetének megtekintése

1. A **Kiszolgálókezelőben**keresse meg a beküldött feladatot.

1. Kattintson az **Adatok** lapra.

1. A **Feladatkimenetek** lapon jelölje be a `"/data.csv"` fájlt.

## <a name="next-steps"></a>További lépések

* [U-SQL-szkript futtatása a munkaállomáson teszteléshez és hibakereséshez](data-lake-analytics-data-lake-tools-local-run.md)
* [C#-kód hibaelhárítása U-SQL-feladatokban az Azure Data Lake Tools for Visual Studio Code használatával](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)

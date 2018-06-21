---
title: Az Azure Data Lake Analytics használatának első lépései a Visual Studióval
description: Ismerje meg, hogyan telepítheti a Data Lake Tools for Visual Studio eszközt, valamint hogyan fejleszthet és tesztelhet U-SQL-parancsfájlokat.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: get-started-article
ms.date: 05/02/2018
ms.openlocfilehash: 0acaace474d62f18b9b6ca4aaae324405a2f43db
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735793"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studiót Azure Data Lake Analytics-fiókok létrehozásához, feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok Data Lake Analytics-szolgáltatásokba való elküldéséhez. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

>[!IMPORTANT]
> A Microsoft javasolja, hogy frissítsen az Azure Data Lake Tools for Visual Studio 2.3.3000.4-es vagy újabb verziójára. A korábbi verziók már nem tölthetők le, és mostantól elavultnak számítanak. 
>
>**Mi a teendőm?**
>
>1. Ellenőrizze, hogy az Azure Data Lake Tools for Visual Studio 2.3.3000.4-esnél korábbi verzióját használja-e. 
>   
>   ![Az eszköz verziószámának ellenőrzése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
>2. Amennyiben egy, a 2.3.3000.4-esnél korábbi verziót használ, az Azure Data Lake Tools for Visual Studiót a letöltőközpont felkeresésével frissítheti: 
>    - [Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 és 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio**: Az Express kivételével minden kiadás támogatott.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK for .NET** 2.7.1-es vagy újabb verzió.  Telepítse a [Webplatform-telepítővel](http://www.microsoft.com/web/downloads/platform.aspx).
* Egy **Data Lake Analytics**-fiók. Fiók létrehozásához lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Az Azure Data Lake Tools for Visual Studio telepítése

Ehhez az oktatóanyaghoz feltétel, hogy telepítve legyen a Data Lake Tools for Visual Studio. Kövesse a [telepítési utasításokat](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Csatlakozás Azure Data Lake Data Lake Analytics-fiókhoz

1. Nyissa meg a Visual Studiót.
2. Nyissa meg a Kiszolgálókezelőt a **Nézet** > **Kiszolgálókezelő** kiválasztásával.
3. Kattintson a jobb gombbal az **Azure** elemre. Ezután kattintson a **Connect to Microsoft Azure Subscription** (Csatlakozás egy Microsoft Azure-előfizetéshez) lehetőségre, és kövesse a megjelenő utasításokat.
4. A Kiszolgálókezelőben válassza az **Azure** > **Data Lake Analytics** elemet. Ekkor megjelenik a Data Lake Analytics-fiókok listája.


## <a name="write-your-first-u-sql-script"></a>Az első U-SQL parancsfájl megírása

A következő szöveg egy igen egyszerű U-SQL parancsfájl. Definiál egy kisebb adatkészletet, és az alapértelmezett Data Lake Store-ba írja az adatkészletet, amely a `/data.csv` nevű fájl.

```
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

### <a name="submit-a-data-lake-analytics-job"></a>Data Lake Analytics-feladat küldése

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.

2. Válassza az **U-SQL projekt** típust, majd kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást egy **Script.usql** fájllal.

3. Illessze be az előző parancsfájlt a **Script.usql** ablakba.

4. A **Script.usql** ablak bal felső sarkában adja meg a Data Lake Analytics-fiókot.

    ![U-SQL Visual Studio-projekt elküldése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. A **Script.usql** ablak bal felső sarkában válassza a **Submit** (Küldés) elemet.
6. Ellenőrizze a **Analytics-fiókot**, majd válassza a **Submit** (Küldés) lehetőséget. Az elküldés után az eredmény megjelenik a Data Lake Tools for Visual Studio Eredmények ablakában.

    ![U-SQL Visual Studio-projekt elküldése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
7. Kattintson a **Refresh** (Frissítés) gombra a feladat legfrissebb állapotának megtekintéséhez. Ha a feladat sikeresen lezajlott, megjelenik a **Job Graph** (Feladat grafikonja), a **Meta Data Operations** (Metaadat-műveletek), a **State History** (Állapotelőzmények) és a **Diagnostics** (Diagnosztika):

    ![U-SQL Visual Studio Data Lake Analytics-feladat teljesítménygrafikonja](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * A **Feladat összegzése** a feladat összegzését jeleníti meg.   
   * A **Feladat részletei** konkrétabb információkat jeleníti meg a feladatról, beleértve a parancsfájlt, az erőforrásokat és a csúcspontokat.
   * A **Feladatgrafikon** a feladat előrehaladását jeleníti meg képi formában.
   * A **Metaadat-műveletek** bemutatja a U-SQL-katalóguson végrehajtott összes műveletet.
   * Az **Adatok** az összes bemenetet és kimenetet jeleníti meg.
   * A **Diagnosztika** részletes elemzést nyújt a feladat végrehajtásához és a teljesítmény optimalizálásához.

### <a name="to-check-job-state"></a>Feladat állapotának ellenőrzése

1. A Kiszolgálókezelőben válassza az **Azure** > **Data Lake Analytics** elemet. 
2. Bontsa ki a Data Lake Analytics-fiók nevét.
3. Kattintson duplán a **Feladatok** elemre.
4. Válassza ki a korábban elküldött feladatot.

### <a name="to-see-the-output-of-a-job"></a>A feladat eredményének megtekintése

1. A Kiszolgálókezelőben keresse meg az elküldött feladatot.
2. Kattintson az **Adatok** lapra.
3. A **Feladatkimenetek** lapon jelölje be a `"/data.csv"` fájlt.

## <a name="next-steps"></a>További lépések

* [U-SQL-szkript futtatása a munkaállomáson teszteléshez és hibakereséshez](data-lake-analytics-data-lake-tools-local-run.md)
* [C#-kód hibaelhárítása U-SQL-feladatokban az Azure Data Lake Tools for Visual Studio Code használatával](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)

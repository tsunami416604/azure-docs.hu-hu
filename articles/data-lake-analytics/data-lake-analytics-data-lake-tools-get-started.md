---
title: "U-SQL-szkriptek fejlesztése a Data Lake Tools for Visual Studio használatával | Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti a Data Lake Tools for Visual Studio eszközt, valamint hogyan fejleszthet és tesztelhet U-SQL-parancsfájlokat."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2017
ms.author: saveenr, yanacai
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 7bbbb08ff635477a88403a3ae6bd3486d31838ef
ms.contentlocale: hu-hu
ms.lasthandoff: 07/19/2017

---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studiót Azure Data Lake Analytics-fiókok létrehozásához, feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok Data Lake Analytics-szolgáltatásokba való elküldéséhez. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).


## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio**: Az Express kivételével minden kiadás támogatott.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK for .NET** 2.7.1-es vagy újabb verzió.  Telepítse a [Webplatform-telepítővel](http://www.microsoft.com/web/downloads/platform.aspx).
* Egy **Data Lake Analytics**-fiók. Fiók létrehozásához lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Az Azure Data Lake Tools for Visual Studio telepítése 

Töltse le és telepítse az Azure Data Lake Tools for Visual Studio eszközt [a letöltőközpontból](http://aka.ms/adltoolsvs). A telepítést követően vegye figyelembe a következőket:
* A **Kiszolgálókezelő** > **Azure** csomópont tartalmaz egy **Data Lake Analytics** csomópontot. 
* Az **Eszközök** menü tartalmaz egy **Data Lake** elemet.

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

## <a name="next-steps"></a>Következő lépések

* [U-SQL-szkript futtatása a munkaállomáson teszteléshez és hibakereséshez](data-lake-analytics-data-lake-tools-local-run.md)
* [Hibakeresés a C#-kódban – U-SQL-feladatok](data-lake-analytics-debug-u-sql-jobs.md)
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)


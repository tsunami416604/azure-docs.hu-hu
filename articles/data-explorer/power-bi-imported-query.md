---
title: 'Gyors útmutató: Az importált lekérdezést a Power bi-ban az Azure Data Explorer adatainak megjelenítése '
description: 'Ebben a rövid útmutató az adatok a Power bi-ban a három lehetőség közül az: lekérdezés importálását az Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: d14de1f25cc432cb2a9fed2149bd0870aa3ce16a
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050584"
---
# <a name="quickstart-visualize-data-using-a-query-imported-into-power-bi"></a>Gyors útmutató: Adatok megjelenítése Power BI-ba importált lekérdezés használatával

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

A Power BI-adatokhoz való csatlakozásról három lehetőséget kínál a Azure Data Explorer: használja a beépített összekötő, importálhat egy lekérdezést az Azure Data Explorer vagy egy SQL-lekérdezést. Ez a rövid útmutató bemutatja, hogyan importálhat egy lekérdezést, hogy az adatok lekérése, és megjelenítheti a Power BI-jelentésekben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a következőkre lesz szüksége:

* Szervezeti e-mail-fiókkal, amely tagja az Azure Active directory, így kapcsolódhat a [Azure adatkezelő súgófürtben](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasszon **ingyenes letöltés**)

* [Az Azure Data Explorer asztali alkalmazás](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Adatok lekérése az Azure Data Explorer

Először hozzon létre egy lekérdezést az Azure Data Explorer asztali alkalmazás, és exportálja a Power bi-ban való használatra. Ezután csatlakozzon az Azure Data Explorer Súgó fürthöz, és betöltheti az adatokat egy részét a *StormEvents* tábla. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Egy böngészőben nyissa meg [ https://help.kusto.windows.net/ ](https://help.kusto.windows.net/) az Azure Data Explorer asztali alkalmazás elindításához.

1. Az asztali alkalmazás a jobb felső lekérdezési ablakba, majd futtassa a következő lekérdezés másolja.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Az eredményhalmaz első néhány sor a következő képhez hasonlóan kell kinéznie.

    ![Lekérdezés eredményei](media/power-bi-imported-query/query-results.png)

1. Az a **eszközök** lapon jelölje be **lekérdezés a Power bi-bA** majd **OK**.

    ![Lekérdezés exportálása](media/power-bi-imported-query/export-query.png)

1. A Power BI Desktopban a a **kezdőlap** lapon jelölje be **adatok lekérése** majd **üres lekérdezés**.

    ![Adatok lekérése](media/power-bi-imported-query/get-data.png)

1. A Power Query-szerkesztőben, az a **kezdőlap** lapon jelölje be **speciális szerkesztő**.

1. Az a **speciális szerkesztő** ablakban, majd válassza ki az exportált lekérdezés beillesztési **kész**.

    ![Beillesztés lekérdezés](media/power-bi-imported-query/paste-query.png)

1. Jelölje ki a fő Power Query-szerkesztő ablakban **hitelesítő adatok szerkesztése**. Válassza ki **szervezeti fiók**, jelentkezzen be, majd válassza ki **Connect**.

    ![Hitelesítő adatok szerkesztése](media/power-bi-imported-query/edit-credentials.png)

1. Az a **kezdőlap** lapon jelölje be **Bezárás és alkalmazás**.

    ![Bezárás és alkalmazás](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>A jelentésben szereplő adatok megjelenítése

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott jelentés ebben a rövid, törölje a Power BI Desktop (.pbix) fájlt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Adatok megjelenítése Power BI-ban az importált lekérdezés használatával](power-bi-sql-query.md)
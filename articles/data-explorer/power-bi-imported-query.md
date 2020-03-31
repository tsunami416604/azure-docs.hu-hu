---
title: Az Azure Data Explorer adatainak megjelenítése Power BI-ban importált Power BI-lekérdezéssel
description: 'Ebből a cikkből megtudhatja, hogy miként használhatja az adatok megjelenítésére szolgáló három lehetőség egyikét a Power BI-ban: lekérdezés importálása az Azure Data Explorerből.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ff156ab3fe74115bce8f7d6bdd3ba47b514f5ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562479"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Adatok megjelenítése a Power BI-ba importált lekérdezéssel

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

Az Azure Data Explorer három lehetőséget biztosít az adatokhoz való csatlakozáshoz a Power BI-ban: használja a beépített összekötőt, importáljon egy lekérdezést az Azure Data Explorerből, vagy használjon SQL-lekérdezést. Ebből a cikkből megtudhatja, hogyan importálhat lekérdezést, hogy adatokat kaphasson le, és megjelenhessen egy Power BI-jelentésben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez a következőkre van szükség:

* Egy szervezeti e-mail fiók, amely az Azure Active Directory tagja, így csatlakozhat az [Azure Data Explorer súgófürthöz.](https://dataexplorer.azure.com/clusters/help/databases/samples)

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasz: **LETÖLTÉS INGYENES)**

* [Az Azure Data Explorer asztali alkalmazása](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Adatok bekéselése az Azure Data Explorerből

Először hozzon létre egy lekérdezést az Azure Data Explorer asztali alkalmazásban, és exportálja a Power BI-ban való használatra. Ezután csatlakozhat az Azure Data Explorer súgófürthöz, és behozhatja a *StormEvents* tábla adatainak egy részét. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. A böngészőben nyissa [https://help.kusto.windows.net/](https://help.kusto.windows.net/) meg az Azure Data Explorer asztali alkalmazást.

1. Az asztali alkalmazásban másolja a következő lekérdezést a jobb felső lekérdezési ablakba, majd futtassa azt.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Az eredményhalmaz első néhány sorának az alábbi képhez hasonlóan kell kinéznie.

    ![Lekérdezés eredményei](media/power-bi-imported-query/query-results.png)

1. Az **Eszközök** lapon válassza a **Lekérdezés a Power BI-ba** lehetőséget, majd **az OK gombot.**

    ![Lekérdezés exportálása](media/power-bi-imported-query/export-query.png)

1. A Power BI Desktop **Kezdőlap** lapján válassza az **Adatok beszerezni,** majd az **Üres lekérdezés lehetőséget.**

    ![Adatok lekérése](media/power-bi-imported-query/get-data.png)

1. A Power Query Editor **Kezdőlap** lapján válassza a **Speciális szerkesztő**lehetőséget.

1. A **Speciális szerkesztő** ablakban illessze be az exportált lekérdezést, majd válassza a **Kész**lehetőséget.

    ![Lekérdezés beillesztése](media/power-bi-imported-query/paste-query.png)

1. A Power Query Editor fő ablakában válassza a **Hitelesítő adatok szerkesztése**lehetőséget. Válassza **a Szervezeti fiók**lehetőséget, jelentkezzen be, majd válassza a **Csatlakozás**lehetőséget.

    ![Hitelesítő adatok szerkesztése](media/power-bi-imported-query/edit-credentials.png)

1. A **Kezdőlap** lapon válassza **a Bezárás & alkalmazása lehetőséget.**

    ![Bezárás és alkalmazás](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Adatok megjelenítése a jelentésben

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkhez létrehozott jelentésre, törölje a Power BI Desktop (.pbix) fájlt.

## <a name="next-steps"></a>További lépések

[Adatok megjelenítése az Azure Data Explorer-összekötő használatával a Power BI-hoz](power-bi-connector.md)
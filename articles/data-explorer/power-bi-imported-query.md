---
title: Adatok megjelenítése az Azure Adatkezelő egy Power BI importált lekérdezéssel
description: 'Ebből a cikkből megtudhatja, hogyan használhatja a következő három lehetőséget az adatok megjelenítésére Power BIban: lekérdezés importálása az Azure Adatkezelő-ból.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ff156ab3fe74115bce8f7d6bdd3ba47b514f5ff5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562479"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Adatmegjelenítés Power BIba importált lekérdezés használatával

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

Az Azure Adatkezelő három lehetőséget biztosít az adatokhoz való kapcsolódáshoz Power BI: a beépített összekötő használata, lekérdezés importálása az Azure-ból Adatkezelő vagy SQL-lekérdezés használata. Ez a cikk bemutatja, hogyan importálhat egy lekérdezést, hogy beolvassa az adatgyűjtést, és megjelenítse azt egy Power BI jelentésben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőkre lesz szüksége:

* Egy olyan szervezeti e-mail fiók, amely az Azure Active Directory tagja, így kapcsolódhat az [azure adatkezelő Súgó fürthöz](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power bi Desktop](https://powerbi.microsoft.com/get-started/) (válassza az **ingyenes letöltés**lehetőséget)

* [Azure Adatkezelő asztali alkalmazás](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Adatok beolvasása az Azure Adatkezelőból

Először létre kell hoznia egy lekérdezést az Azure Adatkezelő asztali alkalmazásban, és exportálni kell a Power BI való használatra. Ezután kapcsolódhat az Azure Adatkezelő Súgó-fürthöz, és az adatok egy részhalmazát helyezheti el a *StormEvents* táblából. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. A böngészőben nyissa meg a [https://help.kusto.windows.net/](https://help.kusto.windows.net/) az Azure adatkezelő asztali alkalmazás elindításához.

1. Az asztali alkalmazásban másolja a következő lekérdezést a jobb felső szintű lekérdezési ablakba, majd futtassa.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Az eredményhalmaz első néhány sorának az alábbi képhez hasonlóan kell kinéznie.

    ![Lekérdezés eredményei](media/power-bi-imported-query/query-results.png)

1. Az **eszközök** lapon válassza a lekérdezés lehetőséget, **hogy Power bi,** majd **az OK gombra**.

    ![Lekérdezés exportálása](media/power-bi-imported-query/export-query.png)

1. Power BI Desktop a **Kezdőlap** lapon válassza az **adatolvasás** , majd az **üres lekérdezés**lehetőséget.

    ![Adatok lekérése](media/power-bi-imported-query/get-data.png)

1. A Power Query szerkesztő **Kezdőlap** lapján válassza a **speciális szerkesztő**lehetőséget.

1. A **speciális szerkesztő** ablakban illessze be az exportált lekérdezést, majd válassza a **kész**lehetőséget.

    ![Lekérdezés beillesztése](media/power-bi-imported-query/paste-query.png)

1. A fő Power Query szerkesztő ablakban válassza a **hitelesítő adatok szerkesztése**lehetőséget. Válassza a **szervezeti fiók**lehetőséget, majd jelentkezzen be, majd válassza a **kapcsolat**lehetőséget.

    ![Hitelesítő adatok szerkesztése](media/power-bi-imported-query/edit-credentials.png)

1. A **Kezdőlap** lapon válassza a **Bezárás & alkalmaz**lehetőséget.

    ![Bezárás és alkalmazás](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Az adatjelentések megjelenítése

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkhez létrehozott jelentésre, törölje a Power BI Desktop (. pbix) fájlt.

## <a name="next-steps"></a>További lépések

[Adatmegjelenítés az Azure Adatkezelő Connector használatával Power BI](power-bi-connector.md)
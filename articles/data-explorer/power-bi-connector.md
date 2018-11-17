---
title: 'Gyors útmutató: Az Azure Data Explorer-összekötő használata a Power bi az adatok megjelenítése'
description: 'Ebben a rövid útmutató való használatra, a három lehetőség közül vizualizációja a Power bi-ban: a Power BI-összekötő az Azure Data Explorer.'
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: 7948d1d765f597bd25d94447ce704b44f3c97a98
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854807"
---
# <a name="quickstart-visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Gyors útmutató: Az Azure Data Explorer-összekötő használata a Power bi az adatok megjelenítése

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

A Power BI-adatokhoz való csatlakozásról három lehetőséget kínál a Azure Data Explorer: használja a beépített összekötő, importálhat egy lekérdezést az Azure Data Explorer vagy egy SQL-lekérdezést. Ez a rövid útmutató bemutatja, hogyan adatokat, és megjelenítheti a Power BI-jelentés a beépített összekötő használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a következőkre lesz szüksége:

* Szervezeti e-mail-fiókkal, amely tagja az Azure Active directory, így kapcsolódhat a [Azure adatkezelő súgófürtben](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasszon **ingyenes letöltés**)

## <a name="get-data-from-azure-data-explorer"></a>Adatok lekérése az Azure Data Explorer

Először csatlakozik az Azure Data Explorer súgófürtben, majd származó adatok egy részét a tenné a *StormEvents* tábla. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. A Power BI Desktopban a a **kezdőlap** lapon jelölje be **adatok lekérése** majd **további**.

    ![Adatok lekérése](media/power-bi-connector/get-data-more.png)

1. Keresse meg *Azure adatkezelő*válassza **Azure Data Explorer (bétaverzió)** majd **Connect**.

    ![Adatok keresése és lekérése](media/power-bi-connector/search-get-data.png)

1. Az **Összekötő előnézete** képernyőn válassza a **Folytatás** lehetőséget.

1. A következő képernyőn a következő információkat az űrlap kitöltésekor.

    ![Fürt-, adatbázis- és táblázatbeállítások](media/power-bi-connector/cluster-database-table.png)

    **Beállítás** | **Érték** | **Mező leírása**
    |---|---|---|
    | Fürt | *https://help.kusto.windows.net* | A súgófürtben URL-CÍMÉT. Egyéb fürtök esetében az URL-cím szerepel az űrlap *https://\<ClusterName\>.\< Régió\>. kusto.windows.net*. |
    | Adatbázis | Hagyja üresen | Egy adatbázis, amely a fürt üzemelteti, amelyhez csatlakozik. Ez egy későbbi lépésben választjuk ki. |
    | Tábla neve | Hagyja üresen | A táblák az adatbázisban, vagy egy lekérdezés egyikét, például "StormEvents | 1000' igénybe. Ez egy későbbi lépésben választjuk ki. |
    | Speciális beállítások | Hagyja üresen | Lehetőségek a lekérdezésekhez, például az eredmény méretének beállítása. |
    | Adatkapcsolati mód | *A DirectQuery* | Meghatározza, hogy a Power BI importálta az adatokat, vagy közvetlenül csatlakozik az adatforráshoz. |
    | | | |

1. Ha már nincs a súgófürtben csatlakozni, jelentkezzen be. Jelentkezzen be szervezeti fiókkal, majd válassza a **Connect**.

    ![Bejelentkezés](media/power-bi-connector/sign-in.png)

1. Az a **kezelő** bontsa ki a **minták** adatbázisra, válassza **StormEvents** majd **szerkesztése**.

    ![Tábla kiválasztása](media/power-bi-connector/select-table.png)

    A tábla megnyílik a Power Query-szerkesztőben, ahol az adatok importálása előtt Ön szerkesztheti a sorokat és oszlopokat.

1. A Power Query-szerkesztőben, jelölje a nyílra a **DamageCrops** oszlop majd **Rendezés csökkenő sorrendben**.

    ![Csökkenő rendezés DamageCrops](media/power-bi-connector/sort-descending.png)

1. Az a **kezdőlap** lapon jelölje be **sorok megtartása** majd **legelső sorok megtartása**. Adjon meg egy értéket a *1000* területén az első 1000 sort a rendezett tábla.

    ![Legelső sorok megtartása](media/power-bi-connector/keep-top-rows.png)

1. Az a **kezdőlap** lapon jelölje be **Bezárás és alkalmazás**.

    ![Bezárás és alkalmazás](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>A jelentésben szereplő adatok megjelenítése

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott jelentés ebben a rövid, törölje a Power BI Desktop (.pbix) fájlt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Power BI-ban az importált lekérdezéssel adatok megjelenítése](power-bi-imported-query.md)
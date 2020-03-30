---
title: Adatok megjelenítése az Azure Data Explorer Power BI-összekötővel
description: 'Ebből a cikkből megtudhatja, hogy miként használhatja az adatok megjelenítésére szolgáló három lehetőség egyikét a Power BI-ban: az Azure Data Explorer Power BI-összekötőjét.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a95d45481bed17e46429e3a22dff4b8cc62354a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560490"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Adatok megjelenítése az Azure Data Explorer-összekötő használatával a Power BI-hoz

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket. Az Azure Data Explorer három lehetőséget biztosít az adatokhoz való csatlakozáshoz a Power BI-ban: használja a beépített összekötőt, importáljon egy lekérdezést az Azure Data Explorerből, vagy használjon SQL-lekérdezést. Ebből a cikkből megtudhatja, hogy a beépített összekötő vel hogyan szerezhet adatokat, és hogyan jelenítheti meg azokat egy Power BI-jelentésben. Az Azure Data Explorer natív összekötő használata a Power BI-irányítópultok létrehozásához egyszerű. A Power BI-összekötő támogatja [az Importálás és a Közvetlen lekérdezés csatlakozási módokat.](https://docs.microsoft.com/power-bi/desktop-directquery-about) Az **importálási** vagy **DirectQuery** módban a forgatókönyvtől, a méretezéstől és a teljesítménykövetelményektől függően irányítópultokat hozhat létre. 

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez a következőkre van szükség:

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Egy szervezeti e-mail fiók, amely az Azure Active Directory tagja, így csatlakozhat az [Azure Data Explorer súgófürthöz.](https://dataexplorer.azure.com/clusters/help/databases/samples)
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasz: **LETÖLTÉS INGYENES)**

## <a name="get-data-from-azure-data-explorer"></a>Adatok bekéselése az Azure Data Explorerből

Először csatlakozzon az Azure Data Explorer súgófürthöz, majd behozza a *StormEvents* tábla adatainak egy részét. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. A Power BI Desktop **Kezdőlap** lapján válassza az **Adatok beszerezni,** majd az **Egyebek**lehetőséget.

    ![Adatok lekérése](media/power-bi-connector/get-data-more.png)

1. Keressen az *Azure Data Explorer*, válassza az Azure Data **Explorer,** majd **a Connect**.

    ![Adatok keresése és lekérése](media/power-bi-connector/search-get-data.png)

1. Az **Azure Data Explorer (Kusto)** képernyőn töltse ki az űrlapot a következő információkkal.

    ![Fürt-, adatbázis- és táblázatbeállítások](media/power-bi-connector/cluster-database-table.png)

    **Beállítás** | **Érték** | **Mező leírása**
    |---|---|---|
    | Fürt | *https://help.kusto.windows.net* | A súgófürt URL-címe. Más fürtök esetében az URL-cím *\<a\>https://\< ClusterName formában van. Régió\>.kusto.windows.net*. |
    | Adatbázis | Hagyja üresen | Azon a fürtön tárolt adatbázis, amelyhez csatlakozik. Ezt egy későbbi lépésben választjuk ki. |
    | Tábla neve | Hagyja üresen | Az adatbázis egyik táblája vagy egy <code>StormEvents \| take 1000</code>lekérdezés, például . Ezt egy későbbi lépésben választjuk ki. |
    | Speciális beállítások | Hagyja üresen | A lekérdezések beállításai, például az eredményhalmaz mérete. |
    | Adatkapcsolati mód | *DirectQuery* | Azt határozza meg, hogy a Power BI importálja-e az adatokat, vagy közvetlenül kapcsolódik-e az adatforráshoz. Ezzel az összekötővel bármelyik lehetőséget használhatja. |
    | | | |
    
    > [!NOTE]
    > **Importálás** idíleménnyel az adatok átkerülnek a Power BI-ba. **DirectQuery** módban az adatok lekérdezése közvetlenül az Azure Data Explorer-fürtből történik.
    >
    > **Importálási** mód használata:
    > * Az adatkészlet kicsi.
    > * Nincs szükség közel valós idejű adatokra. 
    > * Az adatok már összesítve vannak, vagy összesítést hajt végre [a Kusto-ban](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > **DirectQuery** mód használata:
    > * Az adatkészlet nagyon nagy. 
    > * Közel valós idejű adatokra van szüksége.   

1. Ha még nem rendelkezik kapcsolattal a súgófürthöz, jelentkezzen be. Jelentkezzen be szervezeti fiókkal, majd válassza a **Csatlakozás**lehetőséget.

    ![Bejelentkezés](media/power-bi-connector/sign-in.png)

1. A **Navigátor** képernyőn bontsa ki a **Minták adatbázist,** válassza a **StormEvents** lehetőséget, majd **a Szerkesztés**lehetőséget.

    ![Táblázat kijelölése](media/power-bi-connector/select-table.png)

    A tábla megnyílik a Power Query-szerkesztőben, ahol az adatok importálása előtt Ön szerkesztheti a sorokat és oszlopokat.

1. A Power Query Editorban jelölje ki a **DamageCrops** oszlop melletti nyilat, majd **rendezze a csökkenő t.**

    ![Rendezés DamageCrops csökkenő](media/power-bi-connector/sort-descending.png)

1. A **Kezdőlap** lapon válassza a **Sorok megtartása,** majd a **Felső sorok megtartása lehetőséget.** Adjon meg *egy 1000* értéket, hogy bekerülhessen a rendezett tábla első 1000 sorába.

    ![Felső sorok megtartása](media/power-bi-connector/keep-top-rows.png)

1. A **Kezdőlap** lapon válassza **a Bezárás & alkalmazása lehetőséget.**

    ![Bezárás és alkalmazás](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Adatok megjelenítése a jelentésben

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkhez létrehozott jelentésre, törölje a Power BI Desktop (.pbix) fájlt.

## <a name="next-steps"></a>További lépések

[Tippek az Azure Data Explorer-összekötő használata a Power BI számára az adatok lekérdezéséhez](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)

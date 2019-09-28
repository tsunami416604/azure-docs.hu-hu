---
title: Adatmegjelenítés az Azure Adatkezelő Connector használatával Power BI
description: 'Ebből a cikkből megtudhatja, hogyan használható a három lehetőség a Power BIban található adatmegjelenítéshez: az Azure Adatkezelő Power BI-összekötője.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a2ec179321c5d9cb6e9627e397fcb6ae09dc82ed
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349142"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Adatmegjelenítés az Azure Adatkezelő Connector használatával Power BI

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket. Az Azure Adatkezelő három lehetőséget biztosít az adatokhoz való kapcsolódáshoz Power BI: a beépített összekötő használata, lekérdezés importálása az Azure-ból Adatkezelő vagy SQL-lekérdezés használata. Ebből a cikkből megtudhatja, hogyan érheti el és jelenítheti meg Power BI jelentésekben a beépített összekötőt. Egyszerű az Azure Adatkezelő natív összekötő használata Power BI irányítópultok létrehozásához. Az Power BI-összekötő támogatja az [importálási és közvetlen lekérdezési csatlakozási módokat](https://docs.microsoft.com/power-bi/desktop-directquery-about). Az irányítópultokat az **importálási** vagy **DirectQuery** mód alapján hozhatja létre a forgatókönyvtől, a méretezéstől és a teljesítménytől függően. 

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőkre lesz szüksége:

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Egy olyan szervezeti e-mail fiók, amely az Azure Active Directory tagja, így kapcsolódhat az [azure adatkezelő Súgó fürthöz](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power bi Desktop](https://powerbi.microsoft.com/get-started/) (válassza az **ingyenes letöltés**lehetőséget)

## <a name="get-data-from-azure-data-explorer"></a>Adatok beolvasása az Azure Adatkezelőból

Először kapcsolódjon az Azure Adatkezelő Súgó fürthöz, majd a *StormEvents* tábla adatainak egy részhalmazát adja meg. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Power BI Desktop a **Kezdőlap** lapon válassza az adatlekérdezés, majd a **továbbiak**lehetőséget.

    ![Adatok lekérése](media/power-bi-connector/get-data-more.png)

1. Keresse meg az *azure adatkezelőt*, válassza az **Azure adatkezelő** majd a **kapcsolat**lehetőséget.

    ![Adatok keresése és lekérése](media/power-bi-connector/search-get-data.png)

1. Az **Azure adatkezelő (Kusto)** képernyőn töltse ki az űrlapot a következő információkkal.

    ![Fürt-, adatbázis- és táblázatbeállítások](media/power-bi-connector/cluster-database-table.png)

    **Beállítás** | **Érték** | **Mező leírása**
    |---|---|---|
    | Fürt | *https://help.kusto.windows.net* | A Súgó fürthöz tartozó URL-cím. Más fürtök esetében az URL-cím a következő formátumú: *https://\<ClusterName @ no__t-2. @no__t -3Region\>.kusto.windows.net*. |
    | Adatbázis | Hagyja üresen | Azon a fürtön tárolt adatbázis, amelyhez csatlakozik. Ezt egy későbbi lépésben fogjuk kiválasztani. |
    | Tábla neve | Hagyja üresen | Az adatbázis egyik táblája, vagy egy lekérdezés, például <code>StormEvents \| take 1000</code>. Ezt egy későbbi lépésben fogjuk kiválasztani. |
    | Speciális beállítások | Hagyja üresen | A lekérdezésekre vonatkozó beállítások, például az eredményhalmaz méretének beállítása. |
    | Adatkapcsolati mód | *DirectQuery* | Meghatározza, hogy Power BI importálja-e az adatimportálást, vagy közvetlenül kapcsolódik-e az adatforráshoz. Ezzel az összekötővel bármelyik lehetőséget használhatja. |
    | | | |
    
    > [!NOTE]
    > **Importálási** módban a rendszer áthelyezi az adatPower BIba. **DirectQuery** módban az adatok lekérdezése közvetlenül az Azure adatkezelő-fürtből történik.
    >
    > **Importálási** mód használata a esetén:
    > * Az adathalmaz kicsi.
    > * Nincs szüksége a közel valós idejű adatbevitelre. 
    > * Az adatok már összesítve vannak, vagy [összesítést végez a Kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > **DirectQuery** mód használata a esetén:
    > * Az adathalmaz nagyon nagy. 
    > * Közel valós idejű adatbevitelre van szükség.   

1. Ha még nem rendelkezik a Súgó fürthöz való kapcsolódással, jelentkezzen be. Jelentkezzen be egy szervezeti fiókkal, majd válassza a **kapcsolat**lehetőséget.

    ![Bejelentkezés](media/power-bi-connector/sign-in.png)

1. A **navigátor** képernyőjén bontsa ki a **minták** adatbázist, és válassza a **StormEvents** , majd a **Szerkesztés**lehetőséget.

    ![Tábla kiválasztása](media/power-bi-connector/select-table.png)

    A tábla megnyílik a Power Query-szerkesztőben, ahol az adatok importálása előtt Ön szerkesztheti a sorokat és oszlopokat.

1. A Power Query szerkesztőben válassza a **DamageCrops** oszlop melletti nyilat, majd a **csökkenő**sorrendet.

    ![Rendezés DamageCrops csökkenő sorrendben](media/power-bi-connector/sort-descending.png)

1. A **Kezdőlap** lapon válassza a **sorok megtartása** , majd a **legfelső sorok megtartása**lehetőséget. Adja meg a *1000* értéket a rendezett tábla felső 1000 sorában.

    ![Legfelső sorok megtartása](media/power-bi-connector/keep-top-rows.png)

1. A **Kezdőlap** lapon válassza a **Bezárás & alkalmaz**lehetőséget.

    ![Bezárás és alkalmazás](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Az adatjelentések megjelenítése

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkhez létrehozott jelentésre, törölje a Power BI Desktop (. pbix) fájlt.

## <a name="next-steps"></a>További lépések

[Tippek az Azure Adatkezelő-összekötő használatához a Power BI az adatlekérdezéshez](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)

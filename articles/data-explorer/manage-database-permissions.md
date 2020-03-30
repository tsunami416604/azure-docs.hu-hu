---
title: Adatbázis-engedélyek kezelése az Azure Data Explorerben
description: Ez a cikk az Azure Data Explorer adatbázisainak és tábláinak szerepköralapú hozzáférés-vezérléseit ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030093"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Az Azure Data Explorer adatbázis-engedélyeinek kezelése

Az Azure Data Explorer lehetővé teszi az adatbázisokhoz és táblákhoz való hozzáférés szabályozását egy *szerepköralapú hozzáférés-vezérlési* modell használatával. Ebben a modellben a *rendszerbiztonsági tagok* (felhasználók, csoportok és alkalmazások) *szerepkörökhöz*vannak rendelve. A rendszerbiztonsági tagok a hozzárendelt szerepkörök nek megfelelően férhetnek hozzá az erőforrásokhoz.

Ez a cikk ismerteti a rendelkezésre álló szerepköröket, és hogyan rendelhet iszerepkörök et az Azure Portal és az Azure Data Explorer felügyeleti parancsokkal.

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az Azure Data Explorer a következő szerepkörökkel rendelkezik:

|Szerepkör                       |Engedélyek                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Adatbázis-rendszergazda             |Bármit megtehet egy adott adatbázis hatókörében.|
|Adatbázis-felhasználó              |Az adatbázis összes adatát és metaadatát képes olvasni. Ezenkívül táblákat (a tábla táblaadminisztrátorává válás) és függvényeket is létrehozhatnak az adatbázisban.|
|Adatbázis-megjelenítő            |Az adatbázis összes adatát és metaadatát képes olvasni.|
|Adatbázis betöltése          |Adatokat lehet beadni az adatbázis összes meglévő táblájába, de nem lehet lekérdezni az adatokat.|
|Adatbázis-figyelő           |Végrehajthatja a '.show ...' az adatbázis és a gyermekentitások környezetében.|
|Táblázat adminisztrátora                |Bármit megtehet egy adott táblázat hatókörében. |
|Tábla betöltése             |Adatok betöltése egy adott tábla hatókörében, de nem lehet lekérdezni az adatokat.|

## <a name="manage-permissions-in-the-azure-portal"></a>Engedélyek kezelése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg az Azure Data Explorer-fürtöt.

1. Az **Áttekintés csoportban** jelölje ki azt az adatbázist, amelyen kezelni szeretné az engedélyeket.

    ![Adatbázis kiválasztása](media/manage-database-permissions/select-database.png)

1. Válassza **az Engedélyek** lehetőséget, majd a **Hozzáadás lehetőséget.**

    ![Adatbázis-engedélyek](media/manage-database-permissions/database-permissions.png)

1. Az **Adatbázis-engedélyek hozzáadása**csoportban jelölje ki azt a szerepkört, amelyhez a felhasználót hozzá kívánja rendelni, majd válassza ki a **rendszerbiztonsági tagok at.**

    ![Adatbázis-engedélyek hozzáadása](media/manage-database-permissions/add-permission.png)

1. Keresse meg a fő, válassza ki, majd válassza ki a **lehetőséget.**

    ![Engedélyek kezelése az Azure Portalon](media/manage-database-permissions/new-principals.png)

1. Kattintson a **Mentés** gombra.

    ![Engedélyek kezelése az Azure Portalon](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Engedélyek kezelése felügyeleti parancsokkal

1. Jelentkezzen be [https://dataexplorer.azure.com](https://dataexplorer.azure.com)a alkalmazásba, és adja hozzá a fürtöt, ha még nem érhető el.

1. A bal oldali ablaktáblában válassza ki a megfelelő adatbázist.

1. A `.add` paranccsal a rendszertagokat rendelheti a szerepkörökhöz: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Ha felhasználót szeretne hozzáadni az Adatbázis felhasználói szerepkörhöz, futtassa a következő parancsot az adatbázis nevének és felhasználójának helyettesítésével.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    A parancs kimenete a meglévő felhasználók listáját és az adatbázisban hozzárendelt szerepköröket jeleníti meg.
    
    Az Azure Active Directoryra és a Kusto engedélyezési modellre vonatkozó példákat az [Alapelvek és az Identitásszolgáltatók](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers) című témakörben talál.

## <a name="next-steps"></a>További lépések

[Lekérdezések írása](write-queries.md)

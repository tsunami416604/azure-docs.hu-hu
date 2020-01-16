---
title: Adatbázis-engedélyek kezelése az Azure-ban Adatkezelő
description: Ez a cikk az Azure Adatkezelőban található adatbázisok és táblák szerepköralapú hozzáférés-vezérlését ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030093"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Azure-Adatkezelő adatbázis-engedélyeinek kezelése

Az Azure Adatkezelő lehetővé teszi az adatbázisok és táblák elérésének vezérlését *szerepköralapú hozzáférés-vezérlési* modell használatával. Ebben a modellben a *rendszerbiztonsági tag* (felhasználók, csoportok és alkalmazások) vannak hozzárendelve a *szerepkörökhöz*. A rendszerbiztonsági tag a hozzárendelt szerepköröknek megfelelően érheti el az erőforrásokat.

Ez a cikk ismerteti az elérhető szerepköröket, valamint azt, hogyan rendeljen hozzá rendszerbiztonsági tagokat a szerepkörökhöz a Azure Portal és az Azure Adatkezelő felügyeleti parancsaival.

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az Azure Adatkezelő a következő szerepkörökkel rendelkezik:

|Szerepkör                       |Engedélyek                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Adatbázis-rendszergazda             |Egy adott adatbázis hatókörében bármit megtehet.|
|Adatbázis-felhasználó              |Az adatbázis összes adatát és metaadatát képes olvasni. Emellett létrehozhatnak táblákat is (ennek a táblának a tábla rendszergazdája lesz) és a függvényeket az adatbázisban.|
|Adatbázis megjelenítője            |Az adatbázis összes adatát és metaadatát képes olvasni.|
|Adatbázis-feldolgozó          |Az az adatbázisban lévő összes meglévő táblába betöltheti az adatmennyiséget, de nem kérdezi le az adatot.|
|Adatbázis-figyelő           |Végrehajtható ". show..." az adatbázis és a hozzá tartozó alárendelt entitások kontextusában lévő parancsok.|
|Tábla rendszergazdája                |Egy adott tábla hatókörében bármit megtehet. |
|Tábla betöltése             |Egy adott tábla hatókörében tud adatokat betölteni, de nem kérdezi le az adatokat.|

## <a name="manage-permissions-in-the-azure-portal"></a>Engedélyek kezelése a Azure Portalban

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Navigáljon az Azure Adatkezelő-fürthöz.

1. Az **Áttekintés** szakaszban válassza ki azt az adatbázist, amelyben az engedélyeket kezelni szeretné.

    ![Adatbázis kiválasztása](media/manage-database-permissions/select-database.png)

1. Válassza az **engedélyek** lehetőséget, majd **adja hozzá**a elemet.

    ![Adatbázis-engedélyek](media/manage-database-permissions/database-permissions.png)

1. Az **adatbázis-engedélyek hozzáadása**területen válassza ki azt a szerepkört, amelyhez hozzá szeretné rendelni a rendszerbiztonsági tag elemet, majd **válassza a rendszerbiztonsági tag lehetőséget**.

    ![Adatbázis-engedélyek hozzáadása](media/manage-database-permissions/add-permission.png)

1. Keresse meg a résztvevőt, jelölje ki, majd **válassza a lehetőséget**.

    ![Engedélyek kezelése a Azure Portalban](media/manage-database-permissions/new-principals.png)

1. Kattintson a **Mentés** gombra.

    ![Engedélyek kezelése a Azure Portalban](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Engedélyek kezelése felügyeleti parancsokkal

1. Jelentkezzen be [https://dataexplorer.azure.com ba ](https://dataexplorer.azure.com), és adja hozzá a fürtöt, ha még nem érhető el.

1. A bal oldali ablaktáblán válassza ki a megfelelő adatbázist.

1. Az `.add` parancs használatával rendeljen rendszerbiztonsági tagokat a szerepkörökhöz: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Ha felhasználót szeretne hozzáadni az adatbázis felhasználói szerepköréhez, futtassa a következő parancsot, és cserélje le az adatbázis nevét és a felhasználót.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    A parancs kimenete a meglévő felhasználók listáját és az adatbázishoz rendelt szerepköröket jeleníti meg.
    
    A Azure Active Directory és a Kusto engedélyezési modelljére vonatkozó példákért tekintse meg az [alapelvek és az identitás-szolgáltatók](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers) című témakört.

## <a name="next-steps"></a>Következő lépések

[Lekérdezések írása](write-queries.md)

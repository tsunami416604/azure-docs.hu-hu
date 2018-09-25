---
title: Adatkezelő az Azure adatbázis-engedélyek kezelése
description: Ez a cikk azt ismerteti, adatbázisok és táblák az Adatkezelőben az Azure szerepköralapú hozzáférés-vezérlést.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: fbbc5a199116e46aac0874f3dc6d6d9aa18c60cd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954021"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Adatkezelő az Azure adatbázis-engedélyek kezelése

Az Azure Data Explorer lehetővé teszi, hogy ki férhet hozzá az adatbázisokat és táblákat, használatával egy *szerepköralapú hozzáférés-vezérlés* modell. Ez a modell alapján *rendszerbiztonsági tagok* (felhasználók, csoportok és alkalmazások) vannak leképezve *szerepkörök*. Rendszerbiztonsági tagok hozzá vannak rendelve a szerepköröknek megfelelően erőforrások eléréséhez.

Ez a cikk ismerteti az elérhető szerepkörök és rendszerbiztonsági tagok hozzárendelése ezeket a szerepköröket.

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az adatkezelő az Azure rendelkezik a következő szerepkörök:

|Szerepkör                       |Engedélyek                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Adatbázis-rendszergazda             |Bármit egy adott adatbázis hatókörében.|
|Adatbázis-felhasználót.              |Olvashatja az összes adatot és az adatbázis metaadatait. Emellett azok hozhat létre táblák (a tábla rendszergazda számára, hogy a táblázat neve) és a functions az adatbázisban.|
|Adatbázis-megjelenítő            |Olvashatja az összes adatot és az adatbázis metaadatait.|
|Adatbázis módon eredményesen dolgozható          |Képes kiolvasni az adatokat az összes meglévő táblák az adatbázisban, de nem a lekérdezést.|
|Adatbázis unrestrictedviewer|Lekérdezheti a táblákat, amelyek rendelkeznek a **RestrictedViewAccess** házirend engedélyezve van. Más táblák nem lehet lekérdezni.|
|Adatbázis-figyelője           |Az adatbázis és a gyermekentitások kontextusában a ".show..." parancsokat hajthat végre.|
|Tábla rendszergazda                |Bármit egy adott tábla hatókörében. |
|Tábla módon eredményesen dolgozható             |Képes kiolvasni az adatokat egy adott tábla hatókörében, de nem lekérdezheti az adatokat.|

## <a name="manage-permissions-in-the-azure-portal"></a>Az Azure Portalon engedélyek kezelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Keresse meg az Azure Data Explorer fürt.

1. Az a **áttekintése** területen válassza ki az adatbázist, ahol szeretné kezelheti az engedélyeiket.

    ![adatbázis kiválasztása](media/manage-database-permissions/select-database.png)

1. Válassza ki **engedélyek** majd **hozzáadása**.

    ![Adatbázis-engedélyek](media/manage-database-permissions/database-permissions.png)

1. A **adja hozzá az adatbázis-engedélyek**, válassza ki a szerepkört, amelyet szeretne hozzárendelni a rendszerbiztonsági tag, majd **válassza ki a rendszerbiztonsági tagok**.

    ![Adatbázis-engedélyek hozzáadása](media/manage-database-permissions/add-permission.png)

1. Keresse meg az egyszerű, válassza ki, majd **kiválasztása**.

    ![Az Azure Portalon engedélyek kezelése](media/manage-database-permissions/new-principals.png)

1. Kattintson a **Mentés** gombra.

    ![Az Azure Portalon engedélyek kezelése](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Felügyeleti parancsokkal engedélyek kezelése

1. Jelentkezzen be [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com), és adja hozzá a fürtöt, ha nem érhető el már.

1. A bal oldali panelen válassza ki a megfelelő adatbázishoz.

1. Használja a `.add` parancsot a rendszerbiztonsági tagok hozzárendelése szerepkörökhöz: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Felhasználó hozzáadása az adatbázis-felhasználói szerepkörhöz, futtassa a következő parancsot, és cserélje le az adatbázis nevét és a felhasználó.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    A parancs kimenete megjeleníti a meglévő felhasználók listáját, és a hozzá vannak rendelve. a szerepkörök az adatbázisban.

## <a name="next-steps"></a>További lépések

[A lekérdezésírásra összpontosíthat](write-queries.md)
---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a postgres paramétereit Azure Database for PostgreSQL a Azure Portalon keresztül.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: e1b40e3116d56e87a2f397350ef2ba5510e04c0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707693"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal használatával 
A Azure Database for PostgreSQL-kiszolgálók konfigurációs paramétereit a Azure Portal használatával listázhatja, megjelenítheti és frissítheti.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Paraméterek megtekintése és szerkesztése
1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. A **Beállítások** szakaszban válassza ki a **kiszolgáló paramétereit**. Az oldalon a paraméterek, a saját értékek és a leírások listája látható.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Paraméterek áttekintő lapja":::

4. Kattintson a **legördülő** menüre, hogy megtekintse a felsorolt típusú paraméterek lehetséges értékeit, például a client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Paraméterek áttekintő lapja":::

5. Válassza ki vagy vigye a kurzort az **i** (információ) gombra, hogy megtekintse a numerikus paraméterek, például a cpu_index_tuple_cost lehetséges értékeinek tartományát.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="Paraméterek áttekintő lapja":::

6. Ha szükséges, a **keresőmező** használatával Szűkítse le az adott paramétert. A keresés a paraméterek neve és leírása alapján történik.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Paraméterek áttekintő lapja":::

7. Módosítsa a módosítani kívánt paraméterek értékét. A munkamenetekben elvégzett összes módosítás lila színnel van kiemelve. Ha módosította az értékeket, válassza a **Mentés**lehetőséget. Vagy **elvetheti** a módosításokat.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Paraméterek áttekintő lapja":::

8. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Paraméterek áttekintő lapja":::

## <a name="next-steps"></a>Következő lépések
Ismerkedjen meg a következőkkel:
- [A Azure Database for PostgreSQL kiszolgálói paramétereinek áttekintése](concepts-servers.md)
- [Paraméterek konfigurálása az Azure CLI használatával](howto-configure-server-parameters-using-cli.md)

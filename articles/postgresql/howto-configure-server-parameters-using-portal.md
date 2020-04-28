---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a postgres paramétereit Azure Database for PostgreSQL a Azure Portalon keresztül.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74763674"
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
![Paraméterek áttekintő lapja](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Kattintson a **legördülő** menüre, hogy megtekintse a felsorolt típusú paraméterek lehetséges értékeit, például a client_min_messages.
![Számbavétel legördülő lista](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Válassza ki vagy vigye a kurzort az **i** (információ) gombra, hogy megtekintse a numerikus paraméterek, például a cpu_index_tuple_cost lehetséges értékeinek tartományát.
![információ gomb](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Ha szükséges, a **keresőmező** használatával Szűkítse le az adott paramétert. A keresés a paraméterek neve és leírása alapján történik.
![Keresési eredmények](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Módosítsa a módosítani kívánt paraméterek értékét. A munkamenetekben elvégzett összes módosítás lila színnel van kiemelve. Ha módosította az értékeket, válassza a **Mentés**lehetőséget. Vagy **elvetheti** a módosításokat.
![Módosítások mentése vagy elvetése](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![Az összes visszaállítása az alapértelmezett értékre](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>További lépések
Ismerkedjen meg a következőkkel:
- [A Azure Database for PostgreSQL kiszolgálói paramétereinek áttekintése](concepts-servers.md)
- [Paraméterek konfigurálása az Azure CLI használatával](howto-configure-server-parameters-using-cli.md)

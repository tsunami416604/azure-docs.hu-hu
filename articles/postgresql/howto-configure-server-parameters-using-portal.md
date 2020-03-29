---
title: Kiszolgálóparaméterek konfigurálása - Azure portal - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti, hogyan konfigurálhatja a Postgres paraméterek et az Azure Database for PostgreSQL az Azure Portalon keresztül.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763674"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Kiszolgálóparaméterek konfigurálása az Azure Database for PostgreSQL -Single Server szolgáltatásban az Azure portalon keresztül 
Az Azure Database for PostgreSQL-kiszolgáló konfigurációs paramétereit az Azure Portalon keresztül listázhatja, jelenítheti meg és frissítheti.

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy végiglépj ezen az útmutatón, amire szükséged van:
- [Azure-adatbázis PostgreSQL-kiszolgálóhoz](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Paraméterek megtekintése és szerkesztése
1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. A **BEÁLLÍTÁSOK csoportban** válassza a **Kiszolgáló paramétereit**. A lapon a paraméterek, azok értékei és leírásai láthatók.
![Paraméterek áttekintő lapja](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. A **legördülő** gombbal megtekintheti a felsorolt típusú paraméterek, például a client_min_messages lehetséges értékeit.
![Legördülő menü számbavétele](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Az **i** (információ) gomb balgombjának kijelölésével vagy az egérmutatóval megtekintheti az olyan numerikus paraméterek lehetséges értéktartományát, mint cpu_index_tuple_cost.
![információ gomb](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Szükség esetén a **keresőmező** segítségével szűkítse le egy adott paraméterre. A keresés a paraméterek nevén és leírásán található.
![Keresési eredmények](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Módosítsa a módosítani kívánt paraméterértékeket. A munkamenetben végzett összes módosítás lila színnel van kiemelve. Miután módosította az értékeket, válassza a **Mentés**lehetőséget. Vagy **elvetheti** a módosításokat.
![Módosítások mentése vagy elvetése](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Ha új értékeket mentett a paraméterekhez, akkor mindig visszaállíthatja az összeset az alapértelmezett értékekre, ha az **Összes visszaállítása lehetőséget választja az alapértelmezettre**lehetőségre.
![Az összes visszaállítása az alapértelmezettre](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>További lépések
Ismerkedjen meg a következőkkel:
- [A kiszolgálóparamétereinek áttekintése a PostgreSQL Azure Database szolgáltatásában](concepts-servers.md)
- [Paraméterek konfigurálása az Azure CLI használatával](howto-configure-server-parameters-using-cli.md)
